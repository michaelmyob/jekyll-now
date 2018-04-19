---
layout: default
title: "Effective Java Ch.10: Exceptions"
tags:
---

Background:
- During the Payslip Project, my mentor, Guy, recommended that I read the exceptions chapter of _Effective Java_, which is one of the best text books for Java best practices.
- I've been progressively reading this chapter week by week, for about 3 weeks now. I found it sometimes to be a difficult read since it's very in depth and highly technical, meaning I've had to revisit parts of the chapter several times to understand it.

I made the following highlights in the chapter. As I come across exceptions issues in future, I hope to revisit these highlights:

Notes:
> Exceptions are, as their name implies, to be used only for exceptional conditions; they should never be used for ordinary control flow

> use checked exceptions for conditions from which the caller can reasonably be expected to recover

> If a program throws an unchecked exception or an error, it is generally the case that recovery is impossible and continued execution would do more harm than good

> If you believe a condition is likely to allow for recovery, use a checked exception; if not, use a runtime exception.

> there is a strong convention that errors are reserved for use by the JVM to indicate resource deficiencies, invariant failures, or other conditions that make it impossible to continue execution. Given the almost universal acceptance of this convention, it’s best not to implement any new Error subclasses. Therefore, all of the unchecked throwables you implement should subclass RuntimeException

> Because checked exceptions generally indicate recoverable conditions, it’s especially important for them to provide methods that furnish information to help the caller recover from the exceptional condition.

> The easiest way to eliminate a checked exception is to return an optional of the desired result type…The disadvantage of this technique is that the method can’t return any additional information detailing its inability to perform the desired computation.

> when used sparingly, checked exceptions can increase the reliability of programs; when overused, they make APIs painful to use. 

> Reusing standard exceptions has several benefits. Chief among them is that it makes your API easier to learn and use because it matches the established conventions that programmers are already familiar with

> Do not reuse Exception, RuntimeException, Throwable, or Error directly. Treat these classes as if they were abstract.

> Exception: IllegalArgumentException - Occasion for use: Non-null parameter value is inappropriate  
Exception: IllegalStateException - Occasion for use: Object state is inappropriate for method invocation  
Exception: NullPointerException - Occasion for use: Parameter value is null where prohibited  
Exception: IndexOutOfBoundsException - Occasion for use: Index parameter value is out of range  
Exception: ConcurrentModificationException - Occasion for use: Concurrent modification of an object has been detected where it is prohibited    
Exception: UnsupportedOperationException - Occasion for use: Object does not support method  

> higher layers should catch lower-level exceptions and, in their place, throw exceptions that can be explained in terms of the higher-level abstraction. This idiom is known as exception translation


> Always declare checked exceptions individually, and document precisely the conditions under which each one is thrown using the Javadoc throws tag

> do not use the throws keyword on unchecked exceptions

> It is important that programmers using your API are aware of which exceptions are checked and which are unchecked because the programmers’ responsibilities differ in these two cases.


> Failure Info:
it is critically important that the exception’s toString method return as much information as possible concerning the cause of the failure

> do not include passwords, encryption keys, and the like in detail messages

> Unlike a user-level error message, the detail message is primarily for the benefit of programmers or site reliability engineers, when analyzing a failure. Therefore, information content is far more important than readabilit

> One way to ensure that exceptions contain adequate failure-capture information in their detail messages is to require this information in their constructors instead of a string detail message. 

> Generally speaking, a failed method invocation should leave the object in the state that it was in prior to the invocation. A method with this property is said to be failure-atomic.

> on mutable objects, the most common way to achieve failure atomicity is to check parameters for validity before performing the operation. Otherwise, make objects immutable and prevent having to do this check.

> Even where failure atomicity is possible, it is not always desirable. For some operations, it would significantly increase the cost or complexity. That said, it is often both free and easy to achieve failure atomicity once you’re aware of the issue.

> An empty catch block defeats the purpose of exceptions, which is to force you to handle exceptional conditions.

> If you choose to ignore an exception, the catch block should contain a comment explaining why it is appropriate to do so, and the variable should be named ignored


***

[[Top]](#top)
