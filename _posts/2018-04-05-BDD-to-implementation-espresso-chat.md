---
layout: default
title: "Espresso Chat App: BDD To Implementation"
tags: workshops
---

## The challenge of BDD

Background:
- A start was made on the 'Espresso Chat' person to person chat CLI application between myself and Sina.

Process:
- We strictly followed BDD design principles, initially getting regular feedback from our fellow architects in residence, Jon and Jason.
- We found that we focused only on high level behaviours, and as soon as we tried to go 'down a level' into implementation, and we went back up to behaviour again, and then usually received feedback from the architects to delete everything and not worry about implementation.
- We received really good feedback from Jon about our Behavioural structure, focusing only on what behaviours we want from our system initially.
- One thing we really got stuck on was simply passing object displaying the message. We were reluctant to override toString() since the architects didn't recommend this as best practice, so we eventually made a forDisplay() that converted our _Message_ abstraction to a string. We wanted to avoid this because it meant that a _Message_ as a contract would specify that it would always be a string, when a _Message_ might not always be text in future (it might be photos or something else). It seemed unusual that both of us got stuck on this for so long.

`Key Takeaway:` Try different pairing strategies - don't just go around in circles for too long. Pairing is not always pairing 100% of the time; take time to think on your own and then come back to my partner with new ideas.

## Implementing without clear requirements

- Not only this, as soon as we tried to implement parts of our client and server, we started going around in circles, deleting that implementation, heading back up to behaviour again, and then implementing again. It seemed that both of us were confused about how and what to implement, since we would take a path and code something, without knowing the end goal of where we were trying to go.
- Our feedback from Jon was to go back to our client ASAP. 'You don't know what to do! Go back to your PM!'.
- We then went back to our mentor, got [some clear requirements(https://i.imgur.com/rrhaJ2V.jpg). We then knew where we needed to go...

`Key Takeaway:` Always gather requirements from your Product Manager early on (in this case it was Guy acting as the PM), and ask what the highest priority is in those requirements.

`Another Takeaway:` When talking to PM's, they don't know about technical details such as TDD/BDD - keep it non-technical for them so they can help you and you can help them.

- Until, again we got stuck in priority 1, trying an echo server working. It felt like we were going around in circles. Trying a sockets implementation, then it failing, and we still couldn't get any basic logic working. We didn't know how to do it using our current behaviours that we knew the architects were happy with.
- We went back to the architects for advice, and Jason suggested just trying to get a basic 'raw socket client' and 'raw socket server' up and running, not worrying about the behaviours as previously defined (for now).
- This approach worked well for us. It took us a few iterations to get a basic sockets setup with a buffer working between them, with some limitations, but it worked. We were able to demo this to our PM (Guy) in the next meeting.


## The Demo

- Our demo went well with Guy (acting as PM) and my new mentor, Tim. They were happy that we were getting advice from the architects, and that we knew we had a deadline and that we should have had something to show, even if it didn't follow the exact behaviours we defined in BDD, it still showed that a client and server could echo messages to each other via a socket connection, which is what the highest priority was.

`Key Takeaway:` It might not always matter about the process you follow strictly in a sprint. We all have deadlines. Your PM wants something to show, something they can sell to a client, something they can get feedback on. The MVP might often be good enough.

`Another Takeaway:` We need to be comfortable getting advice from mentors and from the architects in residence, and not always taking what's mentioned as the only way forward, because advice may conflict between those parties. We need to make our own decisions and be confident with them.

`Another Takeaway:` In the last meeting, Guy told us to commit to Git often, and we did not. We were committing at the end of each day still, because I think we felt that what we were doing wasn't really actual work, it was a lot of experimentation that we kept deleting. We need to not be afraid of committing more even though it might be deleted later.

***

[[Top]](#top)
