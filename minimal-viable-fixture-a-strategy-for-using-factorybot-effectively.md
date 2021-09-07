# Minimal Viable Fixture - a strategy for using FactoryBot effectively

[FactoryBot](https://github.com/thoughtbot/factory_bot) is an awesome tool for generating fixtures for your tests in ruby. I've been using it with rails and rspec since its FactoryGirl days at several companies. Over the years, I've noticed bad practices that lead to factories that are difficult to use and maintain. In this post, we'll go over a few of these bad practices and solutions to fix them.

### 1. Factories that produce invalid fixtures
The ideal minimum viable fixture (MVF) is one that has just enough valid data for the test and no more. I've seen examples of factories that are outright unusable. You'd need a lot of domain knowledge to prepare a proper test setup. In a large app, this not only makes your tests unnecessarily verbose but also not user friendly to newcomers or other teams.

For starter, when you build a default factory it should at least pass validations and can be saved without additional configuration. I recently ran the following code in an app with 1200+ factories to find out how many of them produce error on create:

```ruby
require 'factory_bot_rails'

bad_factories = []
ActiveRecord::Base.transaction do
  FactoryBot.factories.instance_variable_get('@items').each do |name, _item|
    begin
      FactoryBot.create(name.to_sym)
    rescue
      bad_factories << name
    end
  end

  # rollback to preserve the current state of my db
  raise ActiveRecord::Rollback
end
```

A whopping 376 bad factories were found, almost a third of the factories are invalid with default settings. When I attempt to create from the same factory twice in a row `2.times { FactoryBot.create(name.to_sym) }`, the number rises to 399. Most of these problems originate from hardcoded data that fail uniqueness validation the second time around. We'll come back to this problem in the hardcoded data section below.

But factories should have higher standards than simply passing validations. I believe that factories should maintain realistic data and associations. For example, suppose you're creating a factory for an employee in a company. An employee belongs to a company and also a department, and the department belongs to the company. A good employee factory should ensure that both the employee and department are associated to the same company. In the majority of tests, this requirement probably won't impact the test results. However, when there are test cases that demand realistic associations it'll cause the poor soul who has to deal with it some grief to figure out the bug. But in terms of performance, it means your employee and department factories always inefficiently spin up separate company instances and slow down your test suite.

### 2. Factories that include superfluous data
At the other end of the spectrum is factories that create more data than required. This problem often comes in the form of factories that prematurely create associated objects. The main culprit of this problem is misuses of FactoryBot itself. Pop quiz: can you spot the problem with the following factory?

```ruby
factory(:employee) do
  sequence(:user_id)
  name { 'John Wick' }
  email { 'j.wick@thecontinental.com' }
  company { FactoryBot.create(:company) }
  department { FactoryBot.create(:department) }
end
```

Actually, every single line of this factory is potentially problematic. But the ones relevant to this section are those that create company and department. In this example, we should build the the associated records instead of creating them. The below fix ensures that calling `build(:employee)` won't instantly insert company and department into the db

```ruby
association :company
after(:build) do |employee|
  employee.department ||= build(:department, company: employee.company)
end
```

Another rule of thumb is if the data isn't required, don't include them altogether. For instance, if phone number and associated profile aren't required, then defining them in the base factory incurs extra cost and may interfere with basic test cases. We can always add traits `with_phone` and `with_profile` to enhance the base factory.

### 3. Factories with hardcoded data
#### Foreign keys
Probably the most notorious hardcoded data is associated id. Taking `user_id`, which we assume to be a required piece of data, from the employee factory defined above

```ruby
sequence(:user_id)
```

You may have also seen this common variant
```ruby
user_id { 1 }
```

These implementations are made possible because the Employee model validates presence of the `user_id` (or missing it altogether) instead of simply requiring the `user` association. This design choice implies that you cannot create both user and employee in the same transaction. Furthermore, your employee records can continue operate with invalid user references even when they're removed. In this case we should be using the user factory to ensure there's a valid associated user in your test setup.

```ruby
association :user
```

#### Time
A common source of flakey tests. When time specification is of essence we should ideally compute it from either current time or relevant point in time. Here are a few different approaches to make sure we don't hardcode time.

```ruby
year { Date.today.year }
terminated_at { 2.days.ago }
started_at { offer.created_at + 1.week }
```

#### Data that can fail uniqueness validation or db constraints
When it comes to generating data that are required to be realistic and unique, [Faker](https://github.com/faker-ruby/faker) and SecureRandom are your friends.

```ruby
name { Faker::Name.name }
email { Faker::Internet.unique.email }
token { SecureRandom.hex(20) }
```

If you need to select a valid option from a list, use `sample` to make sure your entire test suite doesn't always choose the same option all the time

```ruby
status { Employee::VALID_STATUSES.sample }
```

### Bonus: stubbing ActiveRecord::Relation queries
This bad practice isn't entirely about FactoryBot. It most often originates from misguided performance zealots. Here's a typical example

```ruby
let(:employee) { build(:employee) }
before { allow(Employee).to receive(:where).and_return([employee]) }
```

Great, we save a few milliseconds by not inserting an employee into the db. But at the cost of leaking implementation details to the test setup. If the implementation changes to query employees using `find_by` then the test stub will have to change. But it gets worse, by stubbing the query we may not be testing the logic that requires the right type of employee in the test setup. Just don't do it, human cycles are more precious than machine cycles.
