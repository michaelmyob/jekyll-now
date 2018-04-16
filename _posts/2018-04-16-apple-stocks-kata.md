---
layout: default
title: "Apple Stocks Kata: Learnings & Challenges"
tags:
---

Background:
- As part of our weekly (correction: now fortnightly) protege pairing sessions, I completed a new activity pairing with Pedro
- The question was as follows:

> Suppose we could access yesterday's stock prices as a list, where:
The indices are the time in minutes past trade opening time, which was 9:30am local time.  
The values are the price in dollars of Apple stock at that time.
So if the stock cost $500 at 10:30am, stock_prices_yesterday[60] = 500.  
Write an efficient function that takes stock_prices_yesterday and returns the best profit t I could have made from 1 purchase and 1 sale of 1 Apple stock yesterday.  
   For example:  
   stock_prices_yesterday = [10, 7, 5, 8, 11, 9]  
  get_max_profit(stock_prices_yesterday)  
  Returns 6 (buying for $5 and selling for $11)  
  No "shorting"—you must buy before you sell. You may not buy and sell in the same time step (at least 1 minute must pass).

Process & Learnings:
- Initially, I started discussing the problem with Sina, as we were working together for our other project and just to think out loud, we started discussing the fact that this is a relatively easy application, but the devil is really in the detail, especially in terms of efficiency.
- We started discussing efficient algorithms that could pass over the input array only once, rather than having to check the profit against all other items for each item.
- `Key Takeaway:` University algorithms class comes in handy at unexpected times. Don't forget to recap those materials often!

- Beginning the pairing with Pedro, I think we went about it in the right way. We first discussed high level design, using a TDD approach.
- We jumped into writing a test and implementation for getting the most minimal number and then ensured that passed, before continuing onto working out the max profit.
- This was a relatively easy algorithm, and passed quickly with the default given array in the question.
- `Key Takeaway:` When you get to a 'stuck' position pairing, stop, go away and tinker with some ideas and then come back to the pair with new ideas. You'll likely progress better.

Issues & Conflicts:
- However, the first issue we came across was deciding what logic should be in place for each of the following situations:
  - What happens when the input array is constantly declining (no profit situation)?
  - What happens if the same profit appears twice for different pairs?
- We spent the most time discussing the second one here, and my view is that once you've made that profit, it shouldn't update afterwards. Although Pedro's view was that the profit _should_ update later if a lower amount appears, because a lower amount of capital would be required as your investment. While this is logical, it is not mentioned in the specification and an assumption would need to be made on this point.
- I believe that the finance principle of the 'time value of money' applies here - if you can make a profit as early as possible, that is more important than having a profit with the lowest initial capital investment. It's a point of debate that we didn't resolve, but I think both solutions have merit.
- For the first situation, I decided to have a go at my own solution for that, make a branch, make a pull request, then have Pedro review it, just to try a new pairing strategy.


***

[[Top]](#top)
