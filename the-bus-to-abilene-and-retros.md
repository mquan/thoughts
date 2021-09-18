# The Bus to Abilene and Retros

*How teams of smart and well-meaning individuals came up with suboptimal solutions*

<div align="center">
  <img width="340" alt="Screen Shot 2021-09-18 at 4 08 25 PM" src="https://user-images.githubusercontent.com/138784/133911044-fae21191-1cfc-432f-b790-183eeb5f92d3.png">
</div>


This comical series of events actually played out in a team that I was recently a part of. Every single person in the team is intelligent, respectful, and pleasant to work with. I'd jump at the opportunity to work with all of them again. The team was solving challenging problems that everyone is passionate about. The product was shipped on schedule and was considered a business success. In many respects, the team was a well-oiled machine, but in the spirit of continuous improvement we held regular team retros which always inspired engaging discussions.

- The team was formed a year into the pandemic. We intentionally set up daily zoom standup as a way to regularly connect fully remote team members
- Retro 1: we're on different timezones and have different work schedule and family responsibilities, it's difficult for some of us to consistently join standups. We already have weekly planning and frequent pair programming. Solution: Let's try slack standup.
- Retro 2: majority of the team feel we're too isolated and wish there were venues for the team to bond. Solution: introducing **social** standup. Yep, we're not going to discuss work during standup.
- Retro 3: Now that we meet and bond regularly, does anyone find value in the slack standup? We code review and pair every day, we already know what everyone's working on. Solution: Let's kill slack standup.
- Retro 4: We had an issue where there were two separate workstreams that ended up building similar solutions. Obviously we'll refactor, but how can we avoid duplicate efforts in the future? Solution: let's have a weekly sync between the leads of the two workstreams.

I transitioned to another team soon after we shipped a major milestone. So I'm not exactly sure what the next series of problems and solutions are. But I recently heard the team now has daily zoom standups :surprised_pikachu:

The individual analyses and solutions, though could be improved, were reasonable. But as a whole it's obvious that we were short-sighted and ended up chasing our tails. In hindsight, every time the issues were brought up we should've asked ourselves whether the problems were real? could we live with them? Or is the treatment worse than the pain?

### The paradox

This situation reminds me of [the bus to Abilene paradox](https://en.wikipedia.org/wiki/Abilene_paradox) that I recently read from the book Quiet by Susan Cain. The details of the paradox isn't as important as its power to invoke re-examination of the current solution. According to the author, it is a well-known paradox in the military. When someone in the chain of command raises the question 'Are we on the bus to Abilene?', it prompts others, including the superiors, to question the path they're on. It's an understood concept that gives you the power to legitimately question authority. Sort of like 'is this premature optimization?' in software development.

According to wikipedia, the paradox was introduced by management expert Jerry B. Harvey in his 1974 article "The Abilene Paradox: The Management of Agreement" as follow:

> On a hot afternoon in Coleman, Texas, the family is comfortably playing dominoes on a porch, until the father-in-law suggests that they take a [50-mile] trip to Abilene for dinner. The wife says, "Sounds like a great idea." The husband, despite having reservations because the drive is long and hot, thinks that his preferences must be out-of-step with the group and says, "Sounds good to me. I just hope your mother wants to go." The mother-in-law then says, "Of course I want to go. I haven't been to Abilene in a long time."
>
> The drive is hot, dusty, and long. When they arrive at the cafeteria, the food is as bad as the drive. They arrive back home four hours later, exhausted.
>
> One of them dishonestly says, "It was a great trip, wasn't it?" The mother-in-law says that, actually, she would rather have stayed home, but went along since the other three were so enthusiastic. The husband says, "I wasn't delighted to be doing what we were doing. I only went to satisfy the rest of you." The wife says, "I just went along to keep you happy. I would have had to be crazy to want to go out in the heat like that." The father-in-law then says that he only suggested it because he thought the others might be bored.
>
> The group sits back, perplexed that they together decided to take a trip which none of them wanted. They each would have preferred to sit comfortably, but did not admit to it when they still had time to enjoy the afternoon.

It's a tale of how a group of participants collectively arrive at a decision that's against their own preferences because they each think it's the wish of the rest of the group. It illustrates how miscommunication within the group may lead its member to prefer following to the group's decision rather than voicing their concerns. Its main difference from groupthink is the participants in the bus to Abilene consciously act against their wishes.

Thinking back to our agile processes, retro meetings precipitate the ideal conditions for teams to take the metaphorical bus to Abilene.

### 1. Retros focus on problems
Or more optimistically put, development teams are improvement-minded. Sure, we often sprinkle retros with elements of celebration, demo, and knowledge sharing, but invariably the problems are always the ones that dominate the majority of discussions. Because we are trained to solve problems, and every team must have some kind of problems, no matter how small they are. And since there are problems, we must solve them to improve ourselves, right?

Actually, the severity of the problem matters. Because minor problems are usually trade-offs for potentially bigger ones had we optimize team processes differently. Discussions in retro often skips this analysis. People gravitate toward the problems and then the solutions. The origin of the problem, its severity, or what could be an alternative outcome of proposed solutions are rarely discussed.

As an avenue for teams to resolve real issues, retros can be detrimental to well-run teams. Team members want to contribute to the discussion and improvement of the team, similar to the father-in-law who made the well-meaning suggestion. However, in well-run teams, the topics raised in retros are likely not real problems. They may not even be problems that the people who raised or voted for them have a problem with or can't workaround. But in order to be thoughtful and a good citizen of the team, team members contribute and rely on the rest of the team to decide if it's a problem worth solving. The rest of the team, individually may not experience the problem themselves or can live with it, but nonetheless participate out of respect for problem raiser and solution providers. Despite having reservations, they go along with what they thought is best for the team. I'm certainly guilty of that in the scenario above as well as many other ocassions.

### 2. Retros encourage isolated analysis
First, retros scopes tend to be narrow with a small lookback window, typically defined by whatever cadence between the retros is. It's a sensible strategy, we should attend to the most urgent problems. Like evicted caches, problems that are 2-3 cycles old and aren't currently raised should be deprioritized. They may already be resolved or not as severe relative to the current set of problems.

We're biased toward improving the status quo which is often uneventful much like the situation that compels the family in the paradox to consider the 50-mile trip. Like the family, we often take for granted how well things are currently going.

When a problem is identified, it's often magnified and analyzed as if it exists in a vaccuum isolated from other team dynamics. This mode of thinking invariably leads to the same issue when analyzing the solution, namely there's never a rigorous and comprehensive cost-benefit analysis of the solution.

### 3. Retros encourage reactive solutions
As throughful and engaging retro discussions can be, they typically fail to take into account externalities introduced by the proposed solutions. The proposed solutions often lack foresights and usually take the form of doing the opposite of the problem. They resemble bug fixes that focus on fixing the immediate errors without weighing the side effects or larger tech debts introduced by the quick & dirty patch.

Instituting team changes should be treated like introducing breaking changes to a public API. Conceptually, that's exactly what they are. A process change is a destructive two step process, 1) modifying/removing some existing processes and 2) adding a new one. Even when the change is purely additive, we often fail to analyze the cost of implementing and enforcing it. In the paradox, the family attempts to solve their boredom by introducing a change:

1) forgoing their comfortable condition at home
2) taking the 50-mile bus ride

As a group, they failed to consider the cost of the hot, dusty, and long bus ride even though at least one of them identified the problem beforehand. They also didn't foresee the bad cafeteria food at their destination. Unlike this one-off experience, team changes often have long lasting consequences. They're more difficult to stop or rollback, and they can inflict damages that are beyond a bad day.

### Conclusion

I'm not going to pretend I know what the solution is. My hunch is a rigorous yet simple framework to comprehensively analyze the problem as well as the solution. For now, I hope we at least stop to rethink when a team member utters, 'Are we on a bus to Abilene?'

