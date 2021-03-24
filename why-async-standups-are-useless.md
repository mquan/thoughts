# Why Async Standups Are Useless

If you work in a team environment then you often need to interactively exchange information with the rest of the team at a regular cadence. In software development, this often comes in the form of standups. Traditionally, standups are synchronous meetings in person or more commonly on video calls now that more teams are fully remote - I’ll classify these as in-person standups from here on out. However, in many teams that I’ve been a part of, there’s a tendency to opt for standups via team messaging apps in the name of productivity - I’ll refer to this format as async standups. 

Ironically, as both IC and lead on multiple teams over the last 10 years I’ve often found projects that utilize async standups to suffer from a lack of engagement and commit more preventable mistakes. Consequently, they moved at a slower pace and often ended up with suboptimal solutions.

### Why not standup?

The main arguments against standups in general are

- **You should already be fully aware of what your teammates are up to via tools you use** (github, jira, trello, etc.)

  This is in theory possible if everyone diligently studies the project board and pull requests. In practice, I’ve yet to find this to be consistently true as most ICs only focus exclusively on their own work.
  
  But even if all team members truly do this, passive awareness is not the ultimate goal. It’s only the means to uncover issues and blockers a single member may not be able to. It’s always less costly to spot problems earlier, and having regular interactive check-ins (in addition to a healthy dose of pairings) facilitate these serendipitous findings. Every time you discover a bug in production, an overlooked use case, a wrong approach in code review, or duplicate effort by members in the same team, ask yourself how these instances can be avoided or discovered earlier. I’ve seen mediocre teams treat them as unavoidable natural parts of software development. I’ve also seen how grateful and energized a teammate was after their team members saved their day by spotting huge deficiencies in their solution during multiple standups.    
  
&nbsp;


- **Standups are oppressive tools used only to serve micro-managers**.

  If your organization is using standup as a tool for bosses to check on people’s progress then you’re doing it wrong. It’s actually quite easy to spot an underperformer, you don’t need a daily list for that. But I find this more often a paranoia harboured by individuals (including myself earlier in my career) who narrowly define success in terms of their individual output instead of performance of the team as a whole. As a team lead, my number one priority is making sure everyone is unblocked and we're moving in the right direction.

  In every team sport, team members constantly huddle to communicate and discuss adjustments whether for a minute during timeouts or just a few seconds between plays. It makes sense for software teams to incrementally evaluate if they’re moving in the right direction in standups (timeouts) even if they have pre and post game meetings. In my opinion, software development is a team sport but the reason there’s resistance to group syncs probably has to do with the next argument against standups or meetings in general.

&nbsp;


- **Standups are disruptive to the maker’s schedule**.

  This is true and is exacerbated when factoring in multiple time zones and differences in everyone’s schedule. I consider this a necessary tradeoff for the benefits standups bring. If there’s a tool out there that allows teams to have effective yet low cost communication while not hammering focus and productivity, I’m all ears.

&nbsp;

### Benefits of in-person standups

The main benefits I derive from in-person standups are
1. Awareness - help the team stay in sync. Even when you don’t find your teammates’ updates immediately useful, they can come in handy in the future
2. More importantly standups provide an interactive forum to identify issues/blockers and course correct

We don’t know what we don’t know. Without a regular sync, it’s very easy for engineers, especially more junior ones, to blissfully think they are not blocked, or that they’re working on the right problem, or their approach is the right one, or the solution does not already exist.

With in-person standups, I regularly experience the following exchange

> **A**: I’m working on X, will be done tomorrow. No blockers
> 
> **B**: have you thought about Y?
> 
> **A**: no, why?
> 
> **B**: we encountered problem Z in a previous project
> 
> **C**: oh yea, I remember that, let’s chat after standup

The low cost of verbal communication allows B and C to interject and surface a potentially overlooked problem quickly. Obviously, it can be a double-edged sword when you’re not disciplined or you have talkative teammates who love to deep dive into everything. This problem can be mitigated by deferring longer discussions until after everyone’s done and into smaller groups.

I’ve yet to consistently experience similar discussions in async standups. Async standups are good at allowing team members to fire off their status. But that’s about it. When standup is an exercise of reporting what you did yesterday and what you’re going to do today, you’ll tend to focus solely on your own work and miss out on learning what your teammates are up to. Even when you’re intentional about reading all your teammates’ updates, the async nature makes the task almost impossible because:

- **Status updates can be delayed over the span of several hours**. Slack allows you to set up daily reminders at a specific time, but updates are still spread out throughout the day. It’s not practical for everyone to keep checking on them. Most people simply send their updates and ignore the thread. It can also be detrimental to your focus when you get notified of a new update every 30 minutes or so. A 15-minute in-person standup at a set time seems like a more reasonable option to me.

- **Text format adds friction and does not encourage interactivity**. On slack, if you want to discuss a specific item, you must refer to it and mention the relevant owners. That’s a lot more friction than in person where you just follow up right after the person mentions it. Moreover, the fear of making the standup thread noisy with your potentially off-topic discussions or ‘dumb questions’ may prevent you from discussing it altogether or at least prompt you to carry the exchange in DMs, which suffer from the next problem

- **The exchange may involve multiple parties to surface a relevant discussion**. In person, when one person gives their updates, others listen in parallel. Even when you zone out, someone else such as a lead may serve as the connector and pull you in when a relevant item is mentioned. On slack, it’s unlikely multiple people happen to focus on the same item at the same time. Moreover, a missing piece of crucial information can end the discussion and hence the opportunity to spot a potential problem earlier. 

So how do we make standups less disruptive and more effective. I found the following tweaks work well

- Schedule standup at the start of day or around logical breaks such as before lunch. This accommodates late risers but also allows the team to get it out of the way early, and there’s still abundant time for action when a potential issue comes up
- Twice a week cadence, reserve 30 minutes even if you don’t use the whole session. This gives everyone time for longer discussions if needed instead of rushing to another meeting.
- Be disciplined about setting a time limit per person. Allow quick followup questions and comments but defer longer ones til later in smaller groups.
- Limit the number of participants such that the main updates won’t take more than 15mins. It’s better to have two separate standups than a single large one. If there are clear partitions, a portion of the team will zone out when the other is talking.

Disagree or have a better approach? I’d love to hear from you.

Final note: I purposely left out async video standups because I haven't personally experienced them. Though I'm not quite sure how async videos don't suffer from the same problems stemming from the lack of interactivity described above. Additionally, the recording component makes me a little uneasy.

