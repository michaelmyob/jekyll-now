---
layout: default
title: "Decorator design pattern"
tags: reading
---

## Decorator?

Background:
- In the last feedback session for our 'Espresso Chat' app, my mentor Guy went one step ahead of us.
- Before he gave requirements for the next phase, he said he had an attempt at creating the task of what he's giving us.
- He made his own server-client setup and explained that he used the Decorator Pattern.
- Having not come across this before, I thought I would try to understand it properly.
- I started reading about it and asked my fellow proteges for their opinions, in particular, Guy and Sol helped me understand this a lot better and pointed me towards some good resources.

Blog Post:  
I've added this to my 'Design Patterns Cheat Sheet' and added the notes here, too:


> __The Decorator Pattern__  
>
> The decorator pattern allows you to simplify the number of classes you need in a particular application, if it needs a combination of those classes to get to the end goal. It essentially lets you add behaviour to an object dynamically at runtime. You enhance the behaviour of an interface without creating lots and lots of implementations.
>
> __Why should we use it?__  
> It’s inefficient for the programmer to create combinations of every possible number of classes you might need to reach a point in your application at runtime. Using a decorator class allows you to add functionality without subclassing every possible combination or permutation. In addition, people argue that using this pattern is useful to adhere to the Single Responsibility Principle.
>
>
> __When should we use it?__  
> When you’re using a number of classes together, perhaps to chain them together to form a particular outcome. It’s usually when there’s a combination needed and you don’t want to define behaviour for every single option in every variation. This simple ‘burger meal’ example is a good way of explaining how the pattern works: [https://stackoverflow.com/a/29217464](https://stackoverflow.com/a/29217464). Other good examples involve pizzas, using decorators for toppings of each pizza, or ice creams, using decorators for toppings on each ice cream.

> __How should we use it?__  
> Create your initial base class as normal. Create a decorator class that does not subclass that base class. Inside the decorator class, use composition (create an instance of the base class); through the constructor accept any class that derives from the base class. Then, create your particular option in the possible combination of options in a new class that subclasses the decorator. Define the functions in that class to be aligned with the original base class functions, but vary them for this particular option. Finally, create instances of particular options and chain them together via an instance of the class that subclasses decorator.



***

[[Top]](#top)
