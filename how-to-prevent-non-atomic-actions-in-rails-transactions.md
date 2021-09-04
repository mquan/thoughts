# How to prevent non-atomic actions in Rails transactions

In Rails applications, we typically wrap database changes that are required to succeed as a single atomic action in an `ActiveRecord::Transaction`. However, transactions can sometimes include actions that are not SQL statements. When these actions are network calls or async processes, they can yield results that are difficult to rollback or worse introduce bugs that are tricky to identify. In this post, we'll look at what problems these non-atomic actions pose and walk through a few examples of how to fix them. We'll also cover [isolator](https://github.com/palkan/isolator), a gem that helps us surface these non-atomic violations.

### A tale of a transaction

Let's take a look at the classic example of Bob sending money to Alice.

```ruby
def create_transfer!(sender:, receiver:, amount:)
  ActiveRecord::Transaction do
    transfer = Transfer.create!(sender: sender, receiver: receiver, amount: amount)
    AccountService.withdraw!(sender, transfer)
    AccountService.deposit!(receiver, transfer)
  end
end

create_transfer!(sender: bob, receiver: alice, amount: 500)
```

This method creates a Transfer record and updates the sender's and receiver's accounts. It makes sense to wrap these operations in a transaction. If we fail to deposit money into Alice's account, then we should not withdraw money from Bob's or create an unprocessed Transfer record. But let's dive into what's going on in the withdraw and deposit methods.

```ruby
class AccountService
  def self.withdraw!(account, transfer)
    account.update!(account.balance - transfer.amount)

    BankWithdrawJob.perform_async(transfer.id)
    AccountMailer.update_account_balance_for_sender(account.id).deliver_now
  end

  def self.deposit!(account, transfer)
    account.update!(account.balance + transfer.amount)

    BankDepositJob.perform_async(transfer.id)
    AccountMailer.update_account_balance_for_receiver(account.id).deliver_now
  end
end
```

Oh no, these methods don't simply update ActiveRecords! They also queue up background jobs and send emails. If we fail to update Alice's balance, then all db changes in the transaction are rolled back as expected. However, our `BankWithdrawJob` is already queued up and the email to Bob cannot be unsent. In this example, the job will fail because it won't be able to find the transfer, which is a better outcome than actually withdrawing money from Bob's account. It's still not a pleasant experience dealing with a confused customer whose records don't match the notification they received.

Moreover, these non-atomic actions can also interfere with the db calls or increase the chances of failures. If a operation, such as a network call or generation of a huge file, takes a really long time to complete then it'll keep your db transaction open for that duration. If there's an error raised when executing non-atomic actions, then the error will trigger the transaction to rollback.

But the troubles don't end there. We observed that background jobs queued inside transactions can sometimes be run before the transaction complete. This either leads to job failures or produces undesirable result because the records aren't in the state we expect them to be. If you find instances of background jobs in your system that sometimes fail and subsequently succeed on retry without other transient behaviors at play, the culprit may be this race condition introduced by jobs enqueued before the transaction committed.


### Fixing non-atomic violations

The most obvious approach is moving them out of the transaction:
```ruby
def create_transfer!(sender:, receiver:, amount:)
  ActiveRecord::Transaction do
    transfer = Transfer.create!(sender: sender, receiver: receiver, amount: amount)
    sender.update!(balance: sender.balance - amount)
    receiver.update!(balance: sender.balance + amount)
  end

  perform_post_transfer_tasks(transfer, sender, receiver)
end

# All non-atomic ops are moved into this method
def perform_post_transfer_tasks(transfer, sender, receiver)
  BankWithdrawJob.perform_async(transfer.id)
  BankDepositJob.perform_async(transfer.id)
  AccountMailer.update_account_balance_for_sender(sender.id).deliver_now
  AccountMailer.update_account_balance_for_receiver(receiver.id).deliver_now
end
```

However, sometimes it may not be desirable to structure your code in such a way. Additionally, your method may be nested in another parent transaction. Suppose the transfer is just a step in an order fulfillment transaction as follow.

```ruby
ActiveRecord::Transaction.do
  order = Order.create!(buyer: bob, seller: alice, product: product, qty: qty)
  create_transfer!(sender: bob, receiver: alice, amount: product.price * order.qty)
  order.fulfill!
end
```

It could be transactions all the way up! Our problem would be solved if we could leave the non-atomic code inside transaction but execute them after the transaction? We know that `ActiveRecord` models can have `after_commit` hooks that are executed after the transaction complete. Wouldn't it be nice if we can use after_commit everywhere? That's exactly the solution provided by the gem, you guessed it, [after_commit_everywhere](https://github.com/Envek/after_commit_everywhere). After installation, you'll be able to safely colocate non-atomic code inside transactions by wrapping them in `after_commit` blocks as follow.

```ruby
class AccountService
  def self.withdraw!(account, transfer)
    account.update!(account.balance - transfer.amount)

    AfterCommitEverywhere.after_commit do
      BankWithdrawJob.perform_async(transfer.id)
      AccountMailer.update_account_balance_for_sender(account.id).deliver_now
    end
  end

  def self.deposit!(account, transfer)
    account.update!(account.balance + transfer.amount)

    AfterCommitEverywhere.after_commit do
      BankDepositJob.perform_async(transfer.id)
      AccountMailer.update_account_balance_for_receiver(account.id).deliver_now
    end
  end
end
```

So far, we've been dealing only with explicit transactions. What about implicit transactions, those that are parts of `Transfer.create!` and `account.update!` calls? Suppose we have these callbacks in the Transfer model and they shouldn't be executed as part of the transaction.

```ruby
class Transfer < ApplicationRecord
  after_create :notify_admin_for_review
  after_save :send_data_for_risk_analysis
end
```

We know that `after_create` and `after_save` are executed before the transaction committed, so simply move them to after_commit hooks instead:

```ruby
class Transfer < ApplicationRecord
  after_create_commit :notify_admin_for_review
  after_commit :send_data_for_risk_analysis, on: [:create, :update]
end
```

As we've seen in this section, the fixes aren't difficult once we identified the problems. But how do we reliably identify the violations in the first place? Surely, we can't rely on developers to consistently spot them during development or worse as bugs in production. I'm glad you asked.

### Isolator

The [isolator](https://github.com/palkan/isolator) gem, maintained by [Vladimir Dementyev](https://github.com/palkan) who is insanely productive and responsive, helps automate the task of detecting these non-atomic actions. It tracks whether we're in a transaction and raises an error when a non-atomic action is invoked. The gem comes with several useful default adapters that can detect http calls, mailers, and background jobs (sidekiq and resque are also supported). You can also add custom adapters to support your application's needs.

Internally, isolator keeps a transactions count for each database. It monitors every SQL statement to detect a beginning or end of a transaction and increments/decrements the count accordingly. Because of this overhead, it's not recommended to use isolator in production. We enable it in test environment and rely on our high test coverage to surface non-atomic violations in CI. Here's a sample output of a violation

<img width="1068" alt="Screen Shot 2021-09-04 at 9 15 44 AM" src="https://user-images.githubusercontent.com/138784/132102010-68080e96-b299-4c29-bc55-ada5e4d2c318.png">

### Summary

In this post, we learned about the harms that non-atomic can cause to your application. We also covered tools such as isolator and after_commit_everywhere that help us detect and fix the problems. These tools have helped us surface hundreds of transactional violations and continue to safeguard our app against non-atomic operations. We hope it provides ideas to help you keep your Rails apps transactionally safe.
