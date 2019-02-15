----
layout: default
title: "HTTP Server over the past 10 days"
tags:
----

## End of protege program task: Build a HTTP Server
### In this post, I discuss my experiences blocking out 10 days to work on the HTTP Server task, required of proteges at the end of their crew rotations, day by day.

---

#### HTTP server task - Cob Spec - Day 1

Crew rotations now finished!

My approach for the next 2 weeks:

Today: Come up with a rough design architecture using the Jon Eaves 3 steps approach. Draw it out on paper. Start writing a failing test.

My concern right now is how do I test a multithreaded application... but I feel like I shouldn’t be worried about that. I need the concepts of what does a HTTP server actually do. Then I’ll get to that.

---

I’m starting with the high level architecture as follows:

```
Server server = new Server(PORT);
    server.start();

    Method method = Method.GET;
    RequestURI requestURI = new RequestURI("/file1");
    ProtocolVersion protocolVersion = new ProtocolVersion("1.1");

    RequestLine requestLine = new RequestLine(method, requestURI, protocolVersion);
    ClientRequest clientRequest = new ClientRequest(requestLine);

    Client client = new Client();
    ServerResponse serverResponse = new ServerResponse(client.request(HOST, PORT, clientRequest));

    expectedServerResponse = Helpers.SimpleGet.getFile1Response();

    assertEquals(expectedServerResponse, serverResponse.contents);
```

I know I need a client request and a server response, and I guess those should come from servers and clients. I have those concerns separated, including all the things that make up a request, that feed into a RequestLine. I’ve used that word because it’s in the HTTP RFC and I’m trying to be domain driven.

I’m thinking right now it’s easier to work with strings where possible but keeping it as ‘OOP’ as I can where it’s appropriate.

I’ve taken this BDD style from Jon Eaves when he was helping Sina and I with our chat app. I feel that what I took most from those discussions was to ignore implementation details here- forget about needed a socket etc, all we need is a client, server, the request, response and assemble those things together above in a way that we should return the right response from a certain request. I’m ignoring multithreading right now but I’m sure I’ll get to that.

---

```
Response response = new Response(client.request(HOST, PORT, request));

I eventually got to implementing this line, and something doesn’t feel right. Passing a request to a response doesn’t feel right. The whole concept of this is that a server should be responding to a client.
On second thoughts, I’m leaving this concept as is but not introducing it into the tests directly, rather, introducing it contained within the objects that I test (more of an integration test). I think it might be good to not have clients and servers involved here because these are the things that will be contained within those clients and servers.
```

---

End of Day 1 reflection:

I have made my first test fail, and then pass, using a BDD style approach that I feel is logical and sensible, without being too implementation-detaily.

However, after introducing some logic to get sockets to work multithreaded at the server level and then have a client connect to that server, it caused a bit of pain. I was a bit rusty with how ExecutorService and Runnable worked with Java’s threading. Eventually I got to a point where my first test passes ok with my semi-hard-coded approach.

My main concern now is that I export the Jar file to CobSpec tests and I get the following error:
```
Connect to localhost:5000 [localhost/127.0.0.1, localhost/0:0:0:0:0:0:0:1] failed: Connection refuse
```

It’s bizarre because I have a main method that creates a server and listens for connections and it works manually, but for some reason not when using the Cob Spec tests. I’m thinking about my approach for tomorrow now- I feel that trying to get this test to work for Cob Spec could lead down a rabbit hole of unknown time -but- If I can’t get that first test to pass, it means that it will be an uphill battle to get the remaining tests to pass, if I continue with my own tests and ignore Cob Specs output.

Finally, for the command line arguments (-p and-d) for port and directory I found `http://commons.apache.org/proper/commons-cli/usage.html` - a CLI library that lets you create custom args for your CLI app. Looks easy to use, will suss it out.

---

### HTTP server task - Cob Spec - Day 2

No progress today: consumed by workshops and meetings

- Sumo Logic workshop with Kepler crew 1.5hrs
- Welcome lunch for new Product Manager 1hr
- Weekly DDD sessions with Matt Ueckerman 1hr
- Backlog grooming for Orbital crew 0.5hr
- Engineering Buzz Session with Sean Van Oessler 1hr
- Financial Reporting Learning Lab discussion 45min


---

### HTTP server task - Cob Spec - Day 3

My approach today:
- Timebox my debugging of getting CobSpec to work to 1hr; then move onto implementing the SimpleGet actual code. It’s possible that it doesn’t work because I’ve hardcoded responses. Although I don’t think I’d get a connection refused error in that case, anyway, will try to fix first thing.

---

Debugging to try to get Cob Spec to not give me a Connection Refused Error:

- I have built a server to accept socket connections from a client on port 5000. I manually run the server and print “request ok” when the server receives a client request and successfully passes it to an IncomingServerRequest thread. I manually run a client and ping the server on that port. It runs successfully. The socket is open, connects, then closes as expected. I try this via IntelliJ and also via the command line via `java-jar` .... (etc),  after exporting my file to a Jar and running it, even with the parameter of `-p 5000` as mentioned in the spec. No issues, all seems to work as expected. I wrote my own integration test that does this automatically and that works perfectly too.
- I now run it on CobSpec. I check that I’ve set up the paths properly which point to my exported Jar and it’s the correct Jar, by going to the exported libs directory and checking with ls-ltr that it’s been updated after being built. It has been updated. Ok so in the Fitnesse UI I go to `HttpTestSuite > FileServerTestSuite > SimpleGet` and run the test. The setup test succeeds, it can find my file, the http browser test succeeds, it can (maybe?) see that `localhost:5000` is ok. The error that fails is for GET /file1. It is a connection refused `Caused by: java.net.ConnectException: Connection refused (Connection refused)` error.
- When I go to the “Execution Log” in the Fitnesse UI, I see:

```
Feb 06, 2019 8:34:23 AM org.apache.http.impl.execchain.RetryExec execute
INFO: I/O exception (java.net.SocketException) caught when processing request to {}->http://localhost:5000: Connection reset

Feb 06, 2019 8:34:23 AM org.apache.http.impl.execchain.RetryExec execute
INFO: Retrying request to {}->http://localhost:5000
```
- I’ve restarted the Fitnesse server on 9090 several times with no changes.
- I’ve restarted my machine with no changes.
- I’ve re-cloned and re-compiled the Fitness repo.
- Honestly I don’t know what to do at this point . I’ve re-read the README on the CobSpec repo and nothing is evident about troubleshooting this particular problem.

---

Right now I’m just going to move on, even though it annoys me that it doesn’t make sense of why I’d be getting a Connection Refused error, I feel that time will be wasted if I move on this further right now. So what I’ll do is build a more-real implementation of getting ‘/file1’ and see what happens in the Fitnesse UI. I don’t know if that will stop the Connection Refused error.

If it doesn’t, I will ask around and see if anyone else encountered this issue as I don’t want to hinder myself from progressing. Because of this roadblock, I now feel that this task will take me way longer than the end of next week. Even though it frustrates me, I’ve accepted that I might not be 100% finished next week as planned. I will need to work on this in future after I’m an associate dev it appears.

---

Spent the morning doing a real implementation of finding ‘/file1’ via a socket. `[Git Commit 2154ee4]`

My simpleGet integration test passes and I’ve debugged to trace through my socket connection locally working ok. I now properly check whether /file1 exists via Java’s “File” interface and properly parse the request from the client to the server, having the server then respond after it’s found or not found that file. For now, I’m just trying to check whether CobSpec (Fitnesse) passes, so I’ve just hard-coded a “HTTP 200 OK” response if the server has successfully found /file1 (happy path only right now).

I’ve now run my new Jar file in Fitnesse
- The Fitnesse UI says “running tests 2/3” and just hangs with no output. At least it’s not a connection refused error like earlier...
- The server set up tests pass like before, but the main part of the get request doesn’t show up. I press Stop Tests and get the following error:

```
Could not complete testing: fitnesse.testsystems.slim.SlimCommunicationException: Could not send/receive data with SUT
```
- I found a Stackoverflow thread with a similar error, and they suggested adding !define slim.port {8085} to your configuration. I tried this and nothing changed.
- I’m trying to figure out now why Fitnesse just hangs with this error. There doesn’t appear to be a threading bug- As it hangs I’m looking for movements in local memory but nothing is increasing on my RAM monitoring and nothing looks unusual on looking at CPU usage.
- I thought maybe it could be a permissions problem locally? I tried giving the entire cob_spec directory and subdirectories 777 (all read, write, execute) access and restarted cob_spec. Still the same error occurs.
- The other thing I was thinking is that the Fitnesse suite knows where the “public/“ directory is via configuration so maybe my program doesn’t need to know about the ‘public’ directory that contains files. I removed this from my file checker. Still the same error occurs.
- Looking on Google for SlimCommunicationException doesn’t reveal much. Not many sites talk about this and when they do, it’s not related to my situation. One site suggested manually making sure your print writer uses flush. I have auto-flush on my server’s PrintWriter. I also tried adding a writer.flush() after my response is sent. Still the same error occurs.
- I tried now to delete the entire cob_spec directory again, re-clone it, and re-compile it, created the configuration again. Still the same error occurs.
- I tried adding a couple of console log outputs around the place to see if I could debug from within Fitnesse and see the output. Fitnesse does not show me any output at all from anything that’s been run from my Jar file, even though it says it’s successfully connected to localhost:5000.
- Because I literally have no idea what else I can do to fix this since Fitnesse doesn’t let me see what its actually doing, I’m going to try building a new project with a basic server that accepts sockets and just prints out hello world to see how I can get Fitnesse to see me.
- I get a client successfully connected to a server via socket when I test manually. I just output a basic console log message on success. I can’t get Fitnesse to output this. It seems that Fitnesse gives me a Connection Refused exception again.
- Not really sure what to do now.

---
- I asked Sina for some advice at lunch time to see if he experienced any issues. Unfortunately he did this task so long ago that he wasn’t able to offer specific advice.
- I thought the one thing I was missing that maybe I was lacking is the handling of the CLI arguments for-p and-d, even though I was defaulting to port 5000 and the `PUBLIC_DIR` env variable. After implementing this with the Apache Commons CLI library, the same error still occurs.
- I still am not sure what to do with this timeout/hanging issue... I’m looking at the Fitnesse user guide but nothing is relevant when I try to debug within the UI, I’m not getting useful error messages that I can do something with.
- I just tried something random, and I seem to have stopped the hanging/timeout issue: I noticed in my Gradle build that I have:

```
jar {
manifest {
attributes 'Main-Class': ‘Main’
}
```
I changed this to

```
jar {
manifest {
attributes 'Main-Class': 'main.Main'
}
```
And now I return to having Connection Refused errors. At least it’s not timing out anymore.

On second thoughts, this doesn’t seem right. I think it should just be Main, as the package name isn’t named ‘main’. I just don’t know how to debug the timeout error. I’ve now tried playing with various different Gradle configurations to see if my Jar wasn’t being compiled properly. I feel like I’ve been through every combination of Jar creation possible with Gradle.

---


### HTTP server task - Cob Spec - Day 4


Today, I feel like I’ve failed in my own abilities as a developer and I’m not feeling good about my progress. I don’t know where I’m going wrong and perhaps I’ve taken the wrong path from the beginning. What I feel is most appropriate is that I will start again. Jon Eaves once taught us not to get attached to our code and not to be afraid to delete it.

In this v2 approach, what I’ll do is:
-  refer back to the RFC 7230 a lot more. I’ve already realised by reading the section on Requests that I wasn’t doing CRLF properly with `\r\n`, I was just doing `\n`. Still doesn’t help me understand why I was getting Connection Refused errors or timeout errors but it helps with correctness.
- Not try the “Simple GET” test, and try one of the other File Server tests. I’m looking at passing the “DirectoryListing” test first which looks like the main test is to get the root directory and ensure that “body has directory contents- public”

My goal for today and tomorrow if I’m being realistic:
- Start a new project from scratch and get the DirectoryListing Cob Spec test to pass.  

---

I decided to slow down and try a new v2 repository first- I wrote a simple server that outputs ‘now listening’ before the socket accept method, and then ‘accepted’ after it was accepted. I ran this manually. I then ran Cob Spec. I got the same Connection Refused error but weirdly, when I run the app manually in IntelliJ, I get the ‘accepted’ message correctly after Cob Spec throws that error. I don’t know if Cob Spec / Fitnesse is unable to run my Jar file still, but anyway, it’s a good step forward that at least I know Fitnesse can call my open port and see something, even if it’s not in the right way.

Next, I went back to RFC 7230, tried to skip through the irrelevant pages and get to the meat of what I’m trying to achieve, which took a couple of hours. I came across two key findings, as follows.

First, some good advice on page 19 about what to do when parsing. I realised that I wasn’t parsing properly before and this has given me much better insight now into what I should do next.

```
The normal procedure for parsing an HTTP message is to read the
start-line into a structure, read each header field into a hash table
by field name until the empty line, and then use the parsed data to
determine if a message body is expected.  If a message body has been
indicated, then it is read as a stream until an amount of octets
equal to the message body length is read or the connection is closed.
```
I also feel now that I should forget about this v2 and go back to my v1 code because I feel like I was actually closer than I originally thought. I’m going to forget about the DirectoryListing test as originally mentioned above, and focus on the HEAD test, because that way I don’t need to worry about the body parsing.

To pass this HEAD test, I need to parse the request line into a structure, which I’ve done, but now I just need to add the ability to parse the remaining header lines, and introduce CRLF line breaks. I’ll scrap my old GET test and come back to that later.

I’m just trying to get the following to pass:

```
head    /
ensure  response code equals    200
ensure  body has no content
```
My main thoughts now are with the response. I know what status to return, but not the remaining header fields in the response, if any. From what I can see on page 29 of the RFC, we may put Content-Length in the response because with a HEAD:

```
A server MAY send a Content-Length header field in a response to a
HEAD request (Section 4.3.2 of [RFC7231]); a server MUST NOT send
Content-Length in such a response unless its field-value equals the
decimal number of octets that would have been sent in the payload
body of a response if the same request had used the GET method.
```
I just looked up some tutorials on CURL that let you just do a HEAD request and I tried it on a few websites.

On second thoughts, I think the other header fields don’t matter for this right now. I think this test might just bet checking the response code when the directory is found. Then for the next part, the response code when the file is not found. Which also leads me to think now we need a way to check files and directories and whether they exist for this test. So what I’m thinking is that we check the URI with whether it has a dot (.) in it, since directories can’t have dots in their descriptions. This should hopefully be enough to distinguish them.

---

I got to a point in refactoring my v1 with the suggestions above (CRLF and HEAD, with a “.” Check for files)- I got to a point where I feel stuck again. The main reason is that when searching for the “/“ root directory, the way that my code is structured is that inside my Client class I have a response, and that response contains a public directory variable now since I needed to pass that down from the server to the request parser, to know what to do with the URI, and in the client I check for that response.

It doesn’t feel right to me that a Client should know about the public directory variable, although encapsulating a response inside a client seems reasonable because clients get responses, I think clients don’t need to know exactly what a response object is exactly.

After feeling extremely frustrated on this, I’ve decided to ask for some general advice from another protege who did this task ~9 months ago. I feel that I needed to do this as a last resort because I’m so stuck. The advice:
- Follow your instinct of starting again- not getting attached to your code is a good thing.
- Whiteboard/write down diagram the approach
- Don’t use readLine() in the BufferedReader, since it may cause issues later when reading the line break between the header and body

Based on this advice, I am going to proceed in starting again working on the v2 repository (`https://github.com/michaelmyob/michael_HTTP_Server_COB_SPEC_v2`)  with a new approach.

---

[insert diagram]

After starting to implement my v2 based on my new diagram, if I run the exported Jar without a test, I still receive  this error in Fitnesse:

```
Could not complete testing: fitnesse.testsystems.slim.SlimCommunicationException: Could not send/receive data with SUT
```
I realised that this is actually harder to do an integration test than expected- the reason- the client needs to wait for a response from the server after it sent its initial request.
This is where I recall a lot of difficulty in testing when Sina & I worked on our Chat App project last year. I think the best way from memory to test this, rather than waiting for the socket buffer to be full and/or delaying client client to wait for a number of seconds until checking the buffer, which is extremely clunky, I feel that stubbing the responses might be a good option. I tried mocking recently in Area51 but haven’t done much stubbing before. If I stub expected responses this might work in terms of testing what I want to be received if a certain request is sent.

I’m feeling extremely demotivated right now, as if I’ve tried everything I can think of. I feel that I need to start fresh again tomorrow as I’ve gone around in a lot of circles today. I feel comfortable that I started again with a v2, but I feel frustrated that I can’t even get one simple test to run, and that Fitnesse is not revealing why it’s giving me these errors.

---

Towards the end of the day today, I’ve been experimenting using curl in my terminal to try and replicate what Fitnesse is doing.

I tried writing a server that just responds to any request by writing to the socket’s PrintWriter buffer ‘HTTP/1.1 200 OK’.

I feel that it’s a bit weird because I print ‘accepted’ after a socket connection is accepted (all works good in the main server when it runs and accepts a client in the server’s console (in IntelliJ), however, in the client (in Curl) I see:

```
$ curl-i localhost:5000/-v

Trying ::1...
TCP_NODELAY set
Connected to localhost (::1) port 5000 (#0)

GET / HTTP/1.1
Host: localhost:5000
User-Agent: curl/7.54.0
Accept: /

Recv failure: Connection reset by peer
stopped the pause stream!
Closing connection 0
```
I keep getting this ‘Connection reset by peer’ error. I’ve googled the error and nothing similar to my problem presented a solution.

----

FINALLY- some success! End of day 4. Felt like I’ve been banging my head against the wall all day with every corner exhausted... Started working on this at 7.30am today. Finally at 5.45pm I got my first Fitnesse test to pass!!!

What have I done in this final hour of of the day?

- I kept using Curl to try and test my server on localhost:5000 and just passed a curl-I localhost:5000-v to try and return a hard-coded response of ‘HTTP/1.1 200 OK’ to any request that comes in.
- I’ve realised that the advice I received earlier was good- using readLine() on the incoming socket’s buffered reader is what was causing the hanging/timeouts. It appears to print out the request and then hangs because ‘null’ isn’t working as a way to check the end of the line. Maybe because here we use CRLF? I’m not 100% sure why, but as soon as I started to explore Java’s Character Arrays (CharBuffer) or char[]  you can read the whole request character by character.
- I just hacked various input checks with BufferedReader methods and see what worked and what didn’t. I realised that I didn’t understand BufferedReader methods as well as I originally thought.
- This now opens me up to many future paths to pass more tests: Now I know how I’ll parse requests, since I can read that CharBuffer into a String, or maybe even use it directly, I’m not sure yet.
- I feel that passing this one test, although it was hacky and incomplete, has given me a lot of confidence to proceed with the remainder of the task. I had to remind myself on the way home that no matter how hard problems get, stick with the problem, remember how lucky we are to be working on software rather than in boring business jobs pushing paperwork, and remember that anything you learn here will make you a stronger developer.


---

### HTTP server task - Cob Spec - Day 5


Reflecting on my approach this week so far, I feel that roadblock after roadblock could have been solved by me just hacking away at getting this to work without a good design in the first place. I guess a lot of devs used to play like that- hack around until it works and just wire it up. The thing is- I feel like TDD really let me down this week. I feel like because I didn’t understand my core library well enough, it meant that when I had to find errors I didn’t know where to look. Also, TDD resources never mention using a third party web UI test suite in addition to your local tests, and how that all fits in.

I’m happy that I’ve made progress in figuring this out to a point that I’m comfortable with progressing now.

Today’s planned approach:

- Pass the test for SimpleHead and SimpleGet with real implementations, and ensure there is a clean design and good tests that use test stubs, as I mentioned yesterday. I won’t check socket streams in my tests because that’s very hard, but I will check expected responses when a particular request is sent. At least that way, I know my business logic works, and it is just then sent via a different method (via a socket stream) in the real implementation, of which is tested via Fitnesse / Cob Spec.

---

Extremely productive day today in completing the SimpleHead tests and getting them all to pass

- I combined testing manually with Curl to ensure my ‘main’ server setup worked, and automated testing to ensure my business logic works
- I introduced log4j now while it’s not too late in the project- it stopped me from going down the console log path for everything
- I came across many mini-roadblocks:
- The first: how to parse the CharBuffer into a data structure. I toyed with reading character by character and stopping at whitespace characters to make new variables, but that got messy,; I then came across an interesting solution on StackOverflow which was to extend BufferedReader’s readLine() method and not have it escape from LF (\n) AND CR (\r), however I thought this might be overkill. I just thought I’ll read everything I have character by character into a Stringbuilder and use that string, split it using Regex and then use the results from that to do my validation. It seemed like the most appropriate option at the time.
- I realised that inside my IncomingServerRequest object, (where the server talks to clients), I was maintaining the idea of having a ‘request handler’ and a ‘response handler’, when in fact, I looked at it objectively- these weren’t handling anything. I decided to really focus on what my classes were doing and split their responsibilities further, rather than having these ‘handlers’ do everything. I settled on the idea of having an HTTPMessageCreator, since it really maps the CharBuffer to a HTTPMessage object. A creator will generate it, and this makes most sense to me. Similar approach with the response- I will have a response generator, and the generator responds based on the HTTPMessage request passed in. That is where the main business logic will sit, of ‘if this is a GET, do this, if it’s a HEAD, do that’.
- I also realised via trial and error when testing for / and /not-here.txt that those responses worked out ok but when I searched for /file1 it didn’t return a 200 as expected, and I realised it’s because file names don’t have to have the dot in them to be files, so now I removed that checking of the “.” as a condition of something being a file, I just use the .isFile() to see if it exists.
- On that point about files, I also realised that I didn’t have to set my public directory as  the unix style of “./public”, just setting it as “public” is what works. I found this out because my automated tests weren’t working as expected when I implemented the file and directory location checking. I used File’s absolute directory output to figure this out.
- I’m trying to use interfaces where possible here too, with the Responder class. I then implemented it with HeadResponder(HTTPMessage message). I feel that I can probably have a different responder on each type of response, since it will always take in a HTTPMessage.
- I also decided to delete all concepts of a “Client” or a “HTTPClient”- I was using this to test originally in my v1 and I clung onto it too much when it wasn’t really needed. This again comes back to the fact that I didn’t want to go down the difficult path of testing actual sockets waiting for responses from a real stream and having my tests time that- I want my tests to test business logic and not setting up network streams etc. Initially, I had a ‘client stub’ made up of a HTTPMessageCreator and a HTTPResponseGenerator. Basically just takes a message and gives a response. Then I realised, actually, this is what the server is doing, I’m mimicking the server’s behaviour. I left this until last because I just didn’t know how to encapsulate it. I initially made it as a private helper method in my SimpleHead test, as a “ServerResponseStub”, but then realised I probably want to use that later for other tests so I brought it out into its own class to be used by other test classes.
- Overall, those were the major things I discovered and implemented today- I also tried to just clean up a lot of my code, remove redundant comments and make it readable where possible, renaming where I thought was worthwhile. In this process, I also learned how to use ‘git commit---amend’ for the first time.


End of week 1 complete; signing off at 4.43pm Friday.
1 Fitnesse / Cob Spec test complete.

---



### HTTP server task - Cob Spec - Day 6


Last week felt like an uphill climb but I feel like a good milestone was hit on Friday by passing a test (finally) and figuring out how Fitnesse reads and runs output from my Jar file.

Today’s goal:
- Pass the simpleGet tests. I think I could aim higher and try to pass a few more tests today, however, I feel that there is some complexity in getting the header row contents and the body contents within my HTTP message, and that will be refactoring that needs to be done very carefully. Currently, my system at its current point doesn’t handle header rows and a body yet. I want to get this right because it sets me up for a lot of future tests.

I also feel that one important piece of learning that I want to come back to is understanding why readLine() caused issues with BufferedReader. I feel that I discarded it because it didn’t work, but I didn’t learn how it worked properly, to understand how to solve a similar issue next time. I want to write a blog post specifically on that, too. Because I think it’s important not just to hack away until something works, but figure out why some things didn’t work, and this is one of them.

TODO: Come back to this at the end of the project.

---

- First, I refactored the location of my source files. Everything was lumped in the Server package, now I’ve split it into different components.
- One thing I’m not sure how to solve yet, as I write my first failing test, is how to obtain the retrieve the correct header contents separately to the body contents. I think I need may need to split my HTTPMessage to have getters for those things, because in Fitnesse we need to compare not only the 200 OK response, but the contents within the file requested too. One thing I’m also not sure of yet is how a body represents the file contents too, is it just text after the line break after the header or anything else too. To investigate.
- Interesting challenge encountered with readLine() yet again-  I tried to read the file contents for my GET using bufferedReader’s readLine(). I outputted file1 and file2 contents, testing manually using Curl. But the Fitnesse test was failing. I opened both files in atom and noticed that file2 has a line break after its contents, whereas file1 does not. I decided to re-use the code I made for reading into a CharBuffer character array, and use that to get the file contents, as I did before with the HTTPMessage reading. This worked a treat and passes the Fitnesse tests.
- A couple of things with this approach so far ^- this was actually relatively quick to discover and debug, manually testing and hacking away at. However, if I wrote a failing test first and went TDD style, it would have been better because I would know that I didn’t introduce any breaking changes from my previous work (in passing the HEAD tests). I just wanted to explore if it was possible going down this hacky path and seeing what would happen. Overall I do agree with old mate Kent Beck that TDD is the better way here, and especially because I feel that as I add more complexity, I want to reduce the duplication that I’ve just introduced and not break all of the previous work I’d done.
- Reflecting on the use of this CharBuffer, one thing I’m not sure how to do most elegantly is to allocate the right amount of space for the buffer to read before it’s been read. It seems like a typical hard computer science problem- how do you allocate memory when you don’t know how much memory to allocate. I’m I using the right structure, or could I just an ArrayList of characters, but then if I do that, I’m unable to use BufferedReader, which introduces a lot of breaking changes. For now, my approach works. I’ve set the buffer to a reasonable maximum but it still feels like a bit of a waste of memory to allocate space you won’t use.

---

Update- halfway through the day I got the SimpleGet tests passing.

- I refactored to get the the HTTPResponseGenerator to use a switch statement. I also tried going down the path of using an enum rather than comparing strings, but this then meant that I might have to change the logic of how the getter in a HTTPMessage is stored. That can be done later I feel as it still reads nicely with it comparing strings in the switch statement. I have only considered the ‘happy path’ here, so I need to ensure that I handle the right response when the responder is not able to parse the message properly and the method is invalid.
- I did a lot of refactoring in my tests too, making it as clear as possible of what the expected and actual responses are for readability purposes. Instead of just typing out a string for expected responses, I decided to set up a StringBuilder so that it’s more obvious to someone reading the code of what each line looks like in the expected response, rather than them having to decipher line breaks in one single line of code.
- I also introduced a “Constants” file where I encapsulate the CRLF for \r\n which can be used in any class now, which makes for more readable code, I believe.

It appears the FourOhFour tests pass in Fitnesse / Cob Spec. I appear to be handling failing GET requests with correct file not found errors.

I will now focus on getting the SimpleOptions tests working. I haven’t come across OPTIONS for a while but it seems straight forward. Just outputs the commands you can use. I’m going to revert back to a TDD approach this time to try doing things a bit more ‘properly’ (as opposed to ‘hacky’...)

---



### HTTP server task - Cob Spec - Day 7


I’m feeling motivated to finish this thing now, no matter what road blocks I encounter. I feel like I’m on a good run.

Yesterday was successful but I felt like I could have done more. For anyone reading this for future proteges at end of your rotations, I recommend blocking yourself out and focusing on this 100%, don’t attend team meetings or other events and just focus. I’m on day 7 now and I don’t think I’m halfway yet. I feel like this will take me ~3 weeks full time at this rate.

Today’s plan:
- This morning I was looking at the other tests that I’d like to pass within the File Server category. I skipped over the OPTIONS command test yesterday because I realised it had an auth component to it, which I’ll do next. I just wanted to see if I could get any other quick wins first.
- I noticed that DirectoryListing and DirectoryLinks tests could be simple to pass, as they just return a body with contents. I was playing around with this earlier.
- For DirectoryListing, I was thinking- ok... How exactly do I even list the directory’s contents. I tried to look up a few examples and test some real sites with Curl, but of course, for security reasons a lot of sites don’t have directory listings available to the public, and Apache lists things in a HTML formatted list with dates and modified times. I just didn’t know what Fitnesse / Cob Spec wanted as a result of the listing. I just tried the most obvious approach. I thought- just to see what Cob Spec might want as a result, I’ll hard code what I think it wants to see and check what the result is. I tried iterating over what was in the “public” directory, and outputting it, that failed- it’s annoying that the test in Cob Spec says “ensure- body has directory contents- public”... so what does that even mean? How do you want the directory contents Mr Test Suite?
- Eventually after trial and error, I just decided to list everything in alphabetical order as a text list:

```
return "HTTP/1.1 200 OK\r\n" + "\r\n" +
"file1" + "\r\n" +
"file2" + "\r\n" +
"image.gif" + "\r\n" +
"image.jpeg" + "\r\n" +
"image.png" + "\r\n" +
"partial_content.txt" + "\r\n" +
"patch-content.txt" + "\r\n" +
"text-file.txt" + "\r\n"
;
```
- I just returned this hard coded result, with the CRLF line breaks, and it passes! So now I get it- it just wanted the body as a text-based list of files. I haven’t seen this before in other web servers so it seems weird that this would be the case, I as I said earlier for security reasons you would never have this listed publicly of course, but that’s ok. It’s relieving to have this test pass.
- I’ll I now focus on creating a real implementation for this and remove the hard coded result. I’ll do this in TDD style, with a failing test first.

---

- Doing a real implementation wasn’t too hard for DirectoryListing. The only small hurdle was to ensure the file name contents are output in alphabetical order, and I used a Java 8 lambda do sort my list of filenames which seemed like a cleaner result.
- I did a bit of refactoring in my GetResponder. I now have clearer paths to execution for a GET. You either are getting a file or getting a directory, and those paths are clear. I still am not 100% happy with the code inside the private methods as of right now. The getFileContents and getDirectoryContents are over 10 lines long each, but it’s not just about the length, I feel that I need to handle exceptions better here, and actually throw an exception up to the appropriate level, maybe the HTTPResponseGenerator, if a directory can’t list contents for whatever reason, or can’t list file contents for whatever reason. I’ll leave this for now and move to the next test though, as this is minor right now and I’m still focusing on happy path.

---

Looking at what to do next, Basic Auth looks a bit tricky. I need to return certain allowable commands on a particular resource. It’s not the parsing of header sections or responses that I’m concerned about, it’s how I make the /logs directory set as a protected directory. My gut instinct is to start my server with a configuration setting of /logs being protected by basic auth and set the allowable fields. I just don’t know where else you’d have this configuration set up, and whether that should be changeable while the server is running? But maybe I’m overthinking that. I think I’ll just try it via configuration.

I also looked at DirectoryLinks. It seems a bit of an usual one... getting the root directory in DirectoryContents wanted a list of files. But now this also wants a list of files that have links to those files, but only for the files that aren’t name as partial-content files? I don’t get why those wouldn’t be listed when they are still files, and how are these links meant to be displayed? Are we talking HTML `<a href>` tags or something else? We aren’t even making a web browser so I don’t know what they even mean by links... anyway...  

I think I need to understand this partial content first before I get to that, so I can see what those files are doing differently not to be listed in the list of linked files, so I’ll give the PartialContent tests a go now.

---




### HTTP server task - Cob Spec - Day 8

At the end of yesterday, I was working on trying to get one simple PartialContent test to work. It’s tricky because now my HTTP request has to extra elements added to it, it’s no longer just one request line, it’s actually:
- Request line
- *New* Other header rows- 0 to many
- *New* Body- Nothing or something

This adds a fair bit more complexity because to parse this correctly and check if it’s a valid request is one feat on its own, but just getting to the happy path is difficult before I even get to that, because of the combinations of 0 to many and/or nothing or something for the other header rows and body, respectively. I might be not going down the path that other people took because once I read my request character by character, I use StringBuilder and Regex to split up my message. I have this all in one big ugly function now, but I’ll definitely split it into smaller parts once I know the test passes- this is TDD after all, and I’ve made one test fail, I need to make it pass now in whatever way I can.

Today’s plan:
- Finish PartialContent tests
- Complete the SimpleOptions tests, except for the auth-related one
- Start thinking about how I can handle auth and the protected logs folder via configuration, now that I know I can parse header rows from the work above in PartialContent, I should be able to re-use that parsing functionality.

---




### HTTP server task - Cob Spec - Day 9

Yesterday I spent a long time refactoring- the most I’ve spent on refactoring so far. I can see how as you add features to a product, you really want to minimise this refactoring. However, that being said, I am still strictly doing TDD, I passed my failing test with crappy code, but it did take about 4 solid hours to clean up and refactor the duplication and messiness in that code after I got it to pass.
- This was mainly in the GetResponder, which has now been split up into many nice little functions in a nice neat sequence, clean code, however, that class is now over 100 lines and I think I should split that into smaller chunks, perhaps have a PartialGetResponder separate to the main GetResponder. However, it is neat and easy to read now.
- The other major refactoring work was in the HTTPMessageCreator, since now I’m trying to read other header rows (the header rows after the first request line) and then the body section. One thing I know I haven’t done correctly yet is read a “content length” header so I know how many bytes the body is. However, right now I just make a 1mb buffer for it. It was difficult because introducing this new functionality broke some old tests, and I realised that when I was doing a partial content get request my automated tests failed because I wasn’t properly checking the otherHeaderRows hashmap conditional properly. I was checking if partial-content was not empty, whereas I should have been checking if it was present in the first place. I used the debugger in IntelliJ to realise this by tracing through line by line. Painful but worth it.

Today’s plan- I feel today I will not progress much. I have the following on today:
- Go shopping with buddy group to buy things to decorate new proteges desks
- Richmond Let’s celebrate
- Sales & Billing Tribe food day
- Decorate new protege desks
- Pair with new proteges and help set up things at their desk

This leaves me with 3pm to 4.30pm to work on HTTP server.

Today, I hope to get the Partial content request tests passing in Cob Spec. When I ran it yesterday at the end of the day, I thought that at minimum the 0 to 4 byte partial content request would work, but it failed in Cob Spec, even though it passes in my automated tests. It’s a bit weird. Need to find out what’s happening.

I feel a bit disappointed that I didn’t get more done earlier but I’m committed to solving this in my own time and finding a way to chip away at this task. Even though it can be frustrating at times, I think it’s an important learning tool for myself. It’s not often we get to build something from scratch and learn from our mistakes. It will be valuable getting feedback on this in future, too.


---

### HTTP server task - Cob Spec - Day 10


Yesterday in the 1.5 hours free that I had open between meetings and helping the new proteges onboarding, I got caught up coaching some of them with their code problem and didn’t feel like starting HTTP server at 4pm.

Today I am equally as distracted, as I have feedback meetings with mentors, coaching discussions with JC on Kotlin, the DDD session with Matt Ueckerman, Chinese NY celebration, and preparation for my MYOB Learning Lab facilitation as I need to deliver an activities plan ASAP to run by the HR team, then I got caught up talking to the team’s BA on project work for next week.

Too much to fit in today besides HTTP... I feel that I should have maybe blocked myself off from sitting with the team, perhaps? I’m not sure. I’ve probably gotten ~20% of my team eaten up in the past 10 days with ‘other/misc’ stuff that’s come up around me.

It’s been 10 days and I feel that I’ve finished about 25% of the tests and functionality, but that was the easier quarter. My plan now is to work on this HTTP server in my own time. The next 3/4 is the harder stuff. I think dedicating exactly 3 nights per week to it would be good. Not on the weekend and not when I have other commitments.  Passing 1 test per week would be a good goal and doesn’t put too much pressure on to deliver.
