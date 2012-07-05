---
  title: The Hustler's Manifesto
  layout: post
---

![](http://media.tumblr.com/tumblr_lu1xhn9Cx31r2t7zo.jpg)

Four years ago, I began my journey of learning all about the Software Craftsmanship/Agile movements. Let me be clear about exactly what I mean when I refer to the craftsmanship/agile movement. I'm referring to the concepts of Test Driven Development, Iteration, Pivotal Tracker, Retrospectives, etc. which, at least in the Ruby community, are basically tenets. So it came as somewhat of a shock to my system to start my own company and find how royally all these tenets have failed me. Let me show you a conversation I had with my cofounder as an example:

> Roshan: "Hey, so this week I think I'm going to implement the landing page for new OfficeHours instructors"
>
> Jared: "Yeah, that sounds good. Let's do that"

2 Days Later:

> Jared: "Hey, let's scrap OfficeHours and do this other thing. We'll need to build a new Rails app from scratch."
>
> Roshan: "ok. Let's do it."

The Software Craftsmanship and Agile movements have in large been driven by the consulting community, and it makes sense for them because they build in a much more controlled environment. Clients change their minds, but it's nothing like the magnitude of code change that a startup pivot involves. Also, at the end of the day the consultant's deliverable is code. The following doesn't work at a startup:

> Finish Pivotal Tracker Queue
>
> ???
>
> Profit

*(see [here](http://www.youtube.com/watch?v=TBiSI6OdqvA) if you have no idea what I'm talking about)*

Process is the nemesis of a startup and the correct dosage of chaos is its nurturing mother. With all of that in mind, I'd like to propose some principles for startup hackers who find themselves too constrained by Agile practices:

##Rule #1: Don't Test (All) Your Code
Certainly don't ever test drive it (it's ok, no one does anything more than pay TDD lip service anyway).

Here's the thing: tests require maintenance just as much as production code. Here's an example: We recently restructured our data model at Niroka to allow multiple "sessions" for a course. This involved adding an abstraction between "courses" and "enrollments", where if I had written unit tests I would have found myself essentially rewriting all of them to respect the new abstraction layer. It probably would have taken longer than implementing the damn feature.

Here's what you do instead: write *integration* tests for the critical parts of your application. For Rails, this means a high-level cucumber test for things like, in our case, "signing up", "creating a class", and "enrolling in a class".  It's still maintenance, but it's a tradeoff we like because it's tedious to manually test those and we really, really care if they break.

The point is, code evolves. It's never "done", so don't write tests that presume it will be static and your interfaces won't change.

##Rule #2: Stop Being Such a Nerd
"Do you prefer git rebase or git merge?"

"Should my spec names be declarative?"

"Is this method supposed to be private or protected?"

Ask yourself: Do the answers to these questions provide any value to myself or my customers, or am I just dorking out hardcore? I don't mean to be derisive, this is the rule I break most often and so I am guilty of dorking out hardcore.

I don't know why this over-analyzation happens, There's something about the programmer's brain that wants to make everything more efficient, and then that brain gets tunnel vision and finds more and more minute ways to be efficient and loses the forest for the trees. We also work in an industry that is obsessed with methodologies and best practices, so it's easy to get caught up in trivialities. [Just build things, motherfucka](http://programming-motherfucker.com/).

##Rule #3: Take Ownership of Your Features
Don't worship the codebase. I had the tendency to treat code like a sacred scroll that should never be tarnished, and it gets in the way of me creating value to our users. A related effect is that coders tend to push off non-programming work in favor of the next feature to implement. We're good at coding, so we try to find reasons to always have something to code. I think a lot of developers do this, but remember:

> Finish Pivotal Tracker Queue
>
> ???
>
> Profit

doesn't work.

Here's how to get around this gotta-code-the-next-feature mentality: take ownership of the features you implement. Instead of spending time developing Feature B, figure out a way to track and quantify the success metrics for Feature A. Use A/B tests, funnels, or solicit user feedback and actually figure out what value Feature A brought to your app. You may be surprised how much work it is to answer a question as simple as "Did this feature bring any value?" The upside is: you own your feature, it's like a little feature-baby that is all your own and all you want is to see your feature's metrics go up and to the right. Through this process, you'll become a more valuable developer by understanding your business and also gain an appreciation for the idea that "software is just a tool".

*Noteworthy: Facebook has a really interesting system for allowing developers to take ownership of their features even with as many devs as they have. From my understanding, it involves letting dev teams take ownership and implement whatever they want as long as they roll it out slowly and have the metrics to validate the feature.*

###Two notes to end on:

A healthy dosage of chaos can go far for your team. It may not optimize your productivity, but we're not machines -- we need some disruption in our environment to stay creative and excited.

Finally, software is a means to an end. Try not to lose sight of that end, or you'll end up just treading water and never moving forward.

Comment on this post at [Hacker News](https://news.ycombinator.com/item?id=3197143)
