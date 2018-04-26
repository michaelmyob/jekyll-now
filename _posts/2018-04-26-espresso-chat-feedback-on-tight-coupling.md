---
layout: default
title: "Espresso Chat: Progressing on iteration 2 - Feedback on tight coupling"
tags:
---

### Background:
- The project continues in iteration 2 (again) [as previously described](https://michaelmyob.github.io/2018/04/20/2018-04-20-espresso-chat-initial-learnings-challenges).
- Feedback on Tuesday Monday 23rd April: This was a larger feedback session with considerable whiteboarding.

### Feedback comments from mentors:
- It's good to see that you focused on making the changes that we suggested last time - avoiding blocking behaviour and cleaning up code
- It's not a good idea to couple the _Server_ and _ServerWorker_ together, so tightly bound 'like a spaghetti' - _Server_'s shouldn't be aware of _ServerWorker_'s
- Passing a _this_ (as in this _Server_) into each _ServerWorker_ is creating a lot of duplicated objects
- Look into creating a new thread which is a "CRUD Service" that the server runs, that does CRUD operations
- ![Whiteboard reference](https://i.imgur.com/ivtaohW.jpg)
- Pass around that service, rather than passing around multiple copies of the server
- Make the service a Singleton - look up examples of Singleton on internet
- This way, you can store your list of clients (in the ConcurrentHashMap) in that service, rather than the server
- Idea: pass the nickname of a client with it initially connecting, rather than connecting and waiting for input, then registering - just don't connect the socket if that nickname is already taken and ask the client to try again with a different nickname.
- Future path:
  - Good design is important: it's important for us to whiteboard our ideas more, and visualise it
  - In future, we will look to turn this into a REST API, we will use Spring, which will make this all very easy, then use _Electron_ to build a desktop GUI

### Progress / Work Completed:
- We focused on what was given as feedback in this last meeting with the mentors, in order to determine our priorities (same as last time, since we received good feedback on that approach).
- We started right away by refactoring by creating the contract (interface) for our CRUD service that stored the list of clients - we realised that by removing the logic from our _ServerWorker_ that we also removed some logic that was duplicated in regards to the lookup, since by referencing to looking up in the _Server_ from the _ServerWorker_, we were doing the same thing twice.
- We also changed the registration to be in the server instead of the workers, to connect that CRUD service to be able to register clients, rather than our old _ChatUtilities_ doing that.
- We then passed the nickname of a _Client_ via its startup argument rather than by starting up and then prompting them for their nickname. This makes it easier to manage storage of the nickname in identifying which _Client_ is connected to each thread on the _Server_ side, since no 'not registered yet' threads are created (in the time it takes for a user to enter a successful nickname).
- We also implemented a way for a client to safely exit the application. We faced some trouble here because we are using an _ExecutorService_ to create each thread, and we initially decided to use _System.exit(0)_ to exit upon the closing of the socket, which we discovered wasn't doing anything on the client side due to the design of the while(true) loops. We also went through a process of discovery in trying to call _Thread.currentThread.interrupt()_ and _ExecutorService.shutdown()_ to try and flag the thread for closing. We eventually got to the point where we realised this was not even needed - we could just close the socket connection upon receiving an exit message. We made a status message (constant) called __SERVER_QUIT_RESPONSE__ that is common to the server and client, so they could easily handle the closing of the connection. We know that by introducing this status message, this is coupling the server and client to a single message, but our thoughts are that it isn't tight coupling, per se.

### My Concerns - Lack Of Tests:
- I still don't feel comfortable with our current setup because to validate that everything is all wired up and working, we are testing it all manually by running a server and client instance via IntelliJ with different configurations.
- We started out with BDD-style development, and by doing that, as previously described in one of my earlier posts, we got stuck in a bit of a development circle of staying at the 'high level' BDD and the lower level sockets implementation.
- We've got to a point now where we're so far deep into implementation, that we've commented out all of our BDD tests, because they don't make much sense in the way the project has evolved, but also, that the feedback we're getting every week has a new focus every time, and often, requirements are changing each week too.
- I feel that there are elements of this, that it's representative of a real world project with constantly changing requirements, quick deadlines, quick feedback, and priorities of all varying level to complete before the next deadline. But I don't think in all of this, that we should be compromising our tests.


***

[[Top]](#top)
