---
layout: default
title: "Espresso Chat: Continuing to refactor"
tags:
---

### Background:
- The project continues in iteration 2 (again)
- Since the last feedback session, there are several things we worked on, as follows:
- We decided to fix a few small things (that turned into bigger issues than we thought)...
- `Key Takeaway:` Focus on the highest priority first
- We still haven't written any tests yet.
- We decided to refactor a lot of our code by renaming a number of classes and interfaces:
![Whiteboard reference](https://i.imgur.com/1bblVYu.jpg)

### Refactoring, refactoring, refactoring...
- Intially, we decided to start refactoring, before we started any tests, because we wanted it to work with the right functionality. I know that sounds like a bit of a vague argument, but I feel that we really needed to test that the system worked manually as we didn't know if by doing these refactoring tasks whether it would be wasted time to write tests before, only to have to change those tests again later.
- We re-organised our project structure to be cleaner, by grouping various classes and interfaces into separate packages.
- We refactored a lot of the names of our classes as per the whiteboard diagram above, and made them more realistic towards what they actually are. For example, we realised that _ServerWorker_ is a bad name because what work is it actually doing, when it's simply checking for client input? Hence, the renaming of it to _ConsoleInputHandler_. We also renamed our CRUD service into two classes, _HashmapDataStoreHandler_ and _HashmapDataStore_ (singleton), in realising that this could be handling a database instead of something in-memory in future. We also encapsulated the single inputstream and outputstream into _MessageChannel_ (formerlly _ClientSocket_) which makes more sense for what it is.
- We realised after we did this, that we have some classes that are doing multiple things. Hence, per the whiteboard diagram above, we decided to have our _ConsoleInputHandler_ (formerly _ServerWorker_) to refrain fron doing any sending, and encapsulated sending behaviour into a _TextMessageSender_ that just sends.

### Individual Coding: Clean Code
- Based on feedback from the last mentoring catch up, I've attempted to do more individual work on the app without pairing. At this stage, I didn't create pull requests for my mentors to review because I wanted Sina's informal review before I deemed it as ok. 
- `Side note:` Sina and I suggested to Tim that perhaps in the next few weeks if the mentors can provide us with two new features to add, so we can individually work on one each and push to the same branch as on a real project.
- I wanted to try applying my learnings from our _Clean Code Book Club_ so I thought I would see if I can make as many functions as possible smaller in the _ChatServer_ and _ChatClient_ and extract logic out to simplify it in terms of its readability. I wanted to make it read as much in plain English as possible, for example, in the server:

```
    private void attemptClientRegistration(Socket incomingConnection, String clientNickname, MessageChannel messageChannel) throws IOException {

        boolean clientCanBeRegistered = dataStoreHandler.addClient(clientNickname, messageChannel);

        if (clientCanBeRegistered) {
            ConsoleInputHandler consoleInputHandler = new ConsoleInputHandler(messageChannel, dataStoreHandler, messageSender);
            numberOfServerThreadsAvailable.submit(consoleInputHandler);
        }
        else {
            Message msg = new TextMessage("server", SERVER_QUIT_RESPONSE);
            messageSender.send(msg, messageChannel);
            incomingConnection.close();
        }
    }
```
and in the client:

```
public void startClient() {

        initialiseIO();

        try {

            checkForIncomingMessages();
            attemptNicknameRegistration();

            while (isRunning) {

                String readFromKeyboardInput = keyboardReader.readLine();

                if (serverHasSentAQuitResponse(readFromKeyboardInput)) {
                    isRunning = false;
                } else {
                    TextMessage messageFromKeyboardInput = new TextMessage(nickname, readFromKeyboardInput);
                    serialiseToServer(messageFromKeyboardInput);
                }
            }
        }
```

### Moving from String passing into object passing (serializable)
- This was one of the requirements that we didn't actually talk about in our feedback with the mentors, but we implicitly knew it was not correct to have our app only pass strings around with the use of a _PrintWriter_ and a _BufferedReader_ between nodes.
- We knew that if we didn't have message passing being done with object serialisiation and deserialisation, it would come back to bite us later, when perhaps we weren't only sending text, but other types of messages are passed.
- Keeping it generic with objects makes it more extensible and makes more sense for this type of app.
- We decided to remove all our _PrintWriter_'s and _BufferedReader_'s and change our streams to _ObjectInputStream_'s and _ObjectOutputStream_'s for the ability to call _.readObject_ and _.writeObject_ (deserialize and serialise, respectively) rather than just reading lines of a message String.
- This ended up breaking a lot of things in our code, and took about a day to fix, but I'm proud to say I did a lot of the debugging work and fixing on my own. It was a great feeling to have it working properly in entirety at the end of the day.
- The main challenge I faced was that in a socket, you can call _.getOutputStream_ or _.getInputStream_ and it's fine. You can't do that on these object specific streams. However, many tutorials had the workaround of forcing you to make a new stream object every time you wanted to access one. For example: _inputStream = new ObjectInputStream(socket.getInputStream())_. While this worked initially, the problem was that you can only do this once - the header of an _ObjectInputStream_ reads the header of the _ObjectOutputStream_ on the other side of the stream to ensure the stream is active, so you therefore MUST use exactly the same stream object every time, and not just make new objects every time you need to use that stream.
- The workaround was to have the stream only once created in the _ChatServer_ and pass it around, but that looked a bit messy based on Sina's informal review, and I agree. So we decided to encapsulate all streams into the _MessageChannel_, which actually makes perfect sense - the channel should be aware of each side of the connection, because by it's very nature, it is a channel.
- It's a huge relief to have this functionality working as intended.

### Next Steps
- As mentioned in my last post a couple of weeks ago, we are in need of some tests to automate us out of manually starting a server and client each time we change something to see if it works. 
- My mentor, Tim, suggested us not going overboard, but at least get some tests of the core functionality working initially so we have something to show in our next few. We can build upon it after that.
- In my reading time, I spent some time reading about Mocks vs Stubs and made some notes in my OneNote. Hopefully this prepares me better for our next stage of testing this thing.