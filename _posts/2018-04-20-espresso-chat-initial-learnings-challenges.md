---
layout: default
title: "Espresso Chat: Progressing on iteration 2"
tags:
---

### Background:
- The project continues in iteration 2 [as previously described](https://michaelmyob.github.io/2018/04/05/BDD-to-implementation-espresso-chat.html).
- Feedback on Tuesday 17th April: This was a relatively short feedback session (more of a status update)

### Feedback comments from mentors:
- There appears to be blocking behaviour - especially with client registration - look into this for when you register two clients at the same time, the server is blocked
- In the hashmap where you store your clients online, the key is fine as the nickname, but instead of storing the value as a socket or inetaddress, encapsulate all logic into a new data structure and use that to store in the value.
- A new socket for each thread is ok for the server, but ensure no blocking behaviour exists if multiple messages are received.
- Use Java 8 features as much as possible - eg. Executors instead of calling new Thread. We can use a threadpool and use Executors for this.
- Because our sockets/streams implement Closable, we can use the try-with-resources feature of Java 8 to simplify our try-catch blocks.
- Good progress so far, but next time: aim for more clean code.
- One idea to consider in future will be to think about broadcasting a message.
- Ensure you have tests, especially for the register method, we could mock a socket, but don't test the Java API, test only the behaviour who've written on your own.

### Progress:
- We then decided to progress by improving our functionality to meet all requirements first, since this was higher priority, then to clean everything up after this.
- We first made several fixes to prevent blocking behaviours: on the client side, by having two threads, one to continually check for incoming messages, and one to read from the keyboard for messages that the client will send - this fixed our former issue of having to press the enter key after a client wanted to receive a message so that the buffer would manually flush.
- On the server side, we moved the logic for registering a client (upon their first connection with the server) to all be completed in the ServerWorker thread, rather than on the main server which is single threaded. This now meant that two clients could register nicknames with the server at the same time. We also refactored to remove the double _while_ loop in the server.
- Following this, we followed the mentors' advice to encapsulate the socket/message sending and receiving in its own class, that we called _ClientSocket_ (not the best name, will improve later). We then removed all uses of our _ChatUtilities_ utility class, which now means that the server and client are less tightly coupled because they don't rely on this central class to both operate.
- We then introduced try-with-resources where we could, and wrapped some of our logic that was in large methods into seperate smaller private methods.

### List all clients online + Disconnect/Quit:

- In my first task coding alone on this project, when Sina was elsewhere, I decided to make two small changes that we discussed earlier. One was allowing a client to query the server on listing all clients online, which was a simple loop which iterated over the server's hashmap. I tried to ensure the server didn't expose its data structure by just returning a String with the list of clients from the server, so that the ServerWorker didn't know about the details of the server's internal workings in this regard.
- I also had an attempt at the logic of when a client decides to quit. I made a poor judgement call here, as I decided to have the server send a _STATUS_QUIT_NOW_ message that the client could read and process as an indicator of quitting. However, this means that the client is now tightly coupled to the server, and if that message were to change in future, the client would also have to be changed. To prevent this, Sina had a great idea (one of many) to just close the socket on the server upon receiving a quit request from the client. By closing the socket, the connection is terminated and the client's while loop ends.

### Challenges faced: "message from ____"

- We spent some time introducing a new feature that we thought would be important, too. This was to send the 'from' nickname in the message too, so that the recipient didn't just see the message that was sent to them, but who it was from as well.
- We got stuck on this for a few hours as we tried using our _ChatUtilities_ class to send the 'from' name within it too, but this did not work for reasons that we are still unsure of, since we introduced logic slowly, then tested manually, and were getting strange behaviour that we couldn't explain.
- We reverted Git back to a few hours earlier before we tried to implement this feature, and then waited until we completed the encapsulation of _ClientSocket_ as described above, then we did this feature again.
- The main challenge, looking back, was that the server (ServerWorker) knows who a message is from, but the client itself didn't know what its own nickname was. This is because it is determining its nickname at runtime dynamically based on what the server is sending it via the socket's stream.
- Our workaround solution: We almost couldn't believe we didn't think of this in the first place - we had the ServerWorker save the client's nickname upon receipt of that nickname registration, and then when the client sends a message, we prefix that nickname to the message. This way, we didn't have to refactor any other logic in other places where we were determining how to send a message over the socket (and add a 'message from' parameter everywhere, for example).

### Pairing with Guy:  
Then, on Thursday 19th April, we also had my mentor, Guy, come to pair with us. The big advantages and learnings that we got from this:

- It's important to have a strict driver and navigator, don't stray from the process.
- _while(true)_ loops are very bad practice and the MYOB linter is not going to allow you to compile if you have this in production code. We talked through how to refactor some of our server logic when it prompts the client to enter a nickname and that nickname is taken, we need to re-prompt the client again. We could have done this with more of a method call step-by-step process, like a message-driven or event-driven process.
- Also, in eliminating _while(true)_ loops, you could use a flag. We used a flag called _isSignedUp_ to follow Java convention with the prefix "is" and so it reads more like plain English later on. Therefore, we adjust the flag's value to true if a client becomes registered, since our register method returns true upon such an occurrence.

### Next Steps:
- We really need to get back to our tests, because we've gone so deep into getting basic functionality to work just to send one message from one client to another, that we have no automation in the way we test whether our system is working. We'll focus on this, as well as cleaning up and refactoring big blocks into smaller methods, and simplifying where possible, trying to incorporate Java 8 features, as Guy recommended.
- We also need to handle the situation where a client exits or is disconnected, that they are removed from the server's hashmap so that they don't appear as 'online' to other users.

***

[[Top]](#top)
