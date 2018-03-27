---
layout: default
title: "Moving on from the Payslip Project"
tags: workshops
---

## A New Beginning: CRUD/REST

Background:
- To take a break from the Payslip Project and get some experience with pair programming, I took the opportunity at one stand-up to pair with Pedro, who wanted to start learning about CRUD and REST operations.
- We thought we'd build a basic API so we could see how this works and what it actually means.

Process:
- We initially started thinking that we could use Docker to store our API, and another Docker container perhaps for a database in something easy to use like MySQL.
- We then realised we had no idea what we were doing with Docker and what was going on, where things were being stored, and generally how to do anything basic in Docker. Yes, we did have the workshop from Tony the other week, but it wasn't for this particular situation.
- We gave up on Docker quickly and just decided to install MySQL locally with Homebrew, just so we could quickly get something up and running.
- Kenneth then suggested we use Postgres because it's more commonly used here rather than MySQL. We agreed, but thought we would work that out later so that we could just write our specification of what our API could do and then move to adding a database.
- It took us days to get all the config working, with numerous stumbling blocks, such as getting a web server to run, having the browser pick the right HTML or JSP file and rendering it, and generally understanding what Java's Servlet library combined with Tomcat actually does. "Hello World" took us a long time.

Mentor Meeting:
- “Time was wasted” - Guy. It’s important that you start new projects knowing what you want to get out of them. The feedback on our start for this was that it was a good idea and good thinking, but the wrong approach and not at the right time. As per Guy’s advice, we’ll pick this up at a later time.
- Guy then asked how I would rate myself in terms of my Java skills now, and compare that to when I first started at MYOB. I mentioned that I felt like a 5/10 when I started and now I feel like a 6.5/10. I feel that I’ve learned a lot in terms of design patterns, immutability, approaches to problem solving, lots of little tips and tricks, but I feel like I’m still lacking in confidence to pick up something new and run with it.
- A new suggestion was made by Guy for my next task. We will be putting the Payslip Project on hold until I’ve built up skills in other areas and become more confident.
- Guy suggested one option being a command line chat app for two or more people to chat to each other, using sockets programming. Another suggestion was a Napster/style file sharing app which was pitched as more complex than the chat app.
- I initially I had no idea what to choose and just having a very high level understanding of the difference between the options, I immediately leaned towards the chat app because I didn’t feel confident enough to pursue the complex idea. My colleague, Sina, was willing to pair with me on the chat app and we got the ball rolling on Friday by pairing immediately.

`Key Takeaway:` Don't jump into trying something in this program without an idea of what you want to get out of it; don't be afraid of asking people for help.


## A (New) New Beginning: "Espresso Chat"

Process:
- Pair Programming is something I was really keen to try on a real project as I was working on my Payslip Project individually whilst observing others pairing every day.
- My colleague, Sina, already had prior experience on a project working with our in house lead architect, Jon Eaves. Sina introduced to me to a new approach to development with TDD, although technically it’s likely more BDD.
- We started by whiteboarding what we thought our system should look like and what each node in the chat app should be able to do. The advice from Jon was: even though your nodes may or may not have server and client behaviour all in one, don’t couple that behaviour together because you’ll get into a big mess. We’re keeping this in mind as we write code.
- Next, this BDD approach really blew my mind. We started by writing a test that fails, but not only that. In that test we defined the behaviour of our system without thinking about implementation details (sockets, etc). We said things like ‘what do we need? A server? Let’s make one. A client. New instance. A channel to communicate. Sure. A method to talk. Yes. And a response once it’s been talked to. Yes.”
- We are using interfaces too in a great way. For example, a server might be an HTTP Server or an FTP server or any other protocol. So we thought deeply about the “contract” we want  to specify for the behaviour of our server, regardless of protocol.
- We just wrote one test, then we just wrote code to make that test pass. It was really simple and effective. And you can really see what’s going on without getting into messy implementation details so early.
- Another bit of Jon advice: the less code the better (...because he will make you delete it all and start again otherwise).
= Jon suggested we watch the [Ian Cooper video on TDD](https://www.youtube.com/watch?v=EZ05e7EMOLM&feature=youtu.be) before we wrote our second test, and there were some insights into that too that cemented my knowledge of what this approach is trying to get at - it’s not about replacing unit tests. It’s about not making your tests be a burden if you change implementation details further down the line. It’s about starting from a high level and working your way down, and it’s about having flexibility

`Key Takeaway:` Never jump to implementation straight away, even if it's something similar to what you've worked on in a previous project. TDD/BDD is a mindset change, and it takes practise. Get feedback from people straight away.

***

[[Top]](#top)
