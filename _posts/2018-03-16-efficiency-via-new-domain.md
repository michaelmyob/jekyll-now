---
layout: default
title: "Efficiency improvement with a new domain model"
tags: project_learnings payslip_project
---

## Payslip Project

Further progress made on iteration 3 of my payslip project:

#### Refactoring a function away and into its own separate domain [Efficiency Improvement]:
- Background:
  - Within my payslip project, I expressed to my mentors how I felt that my _PayslipCalculator_ class was somewhat inefficient and didn't have a good 'feel' to the way it set the lower bound and upper bound for the tax rate calculations for each employee in question.
  - My algorithm was looking at each employee passed in one by one, getting the annual salary, passing that to a function that then looked at what tax bracket that should fit in, by searching each tax bracket from bottom to top (ie. $0 to $18,200 being bracket 1, $18,201 to $37,000 being bracket 2, etc). This worked correctly, but perhaps not as efficiently as it could be.
  - Although this is a small exercise, we should always consider scale, and therefore, inefficient code becoming a major issue.
- Refactored Solution:
  - I didn't get direct feedback from my mentors about this factor, other than to think about making a separate domain model specifically for this issue.
  - I decided to make a new class called _EmployeeTaxAttributes_ that just handled this lower/upper bound setting behaviour, and removing that function from the _PayslipCalculator_.
  - After doing some research online about efficiently searching between bounds, specifically for a range, I came across Java's inbuilt method by calling __Arrays.binarySearch__. It brought back memories of binary search trees from Algorithms classes at University after seeing how it's implemented.
  - I have now got the upper/lower bounds check being performed within an array that's set by pulling from the tax rates input file, and those bounds are filled in a simple Java array.
  - When an employee salary is passed through, it then performs an efficient binary search on that array and returns either the index of where it should be (ie. its correct tax bracket) or the index that it is actually located at (if the taxable income is exactly the amount that is equal to the bound amount).
  - I may be incorrect here, but I believe this has reduced the bounds search complexity time from __O(n)__ to __O(log n)__.

    ```go
    public void calculateTaxRateBoundsBasedOnSalaryInput() {
      int annualSalaryInt = Integer.parseInt(annualSalary.toString());
      int search = Arrays.binarySearch(taxRateBounds,annualSalaryInt);

      int indexOfSearch = search;

      if (search < 0) {
          indexOfSearch *= -1;
          indexOfSearch -= 1;
      }

      setFixedVariableDollarsAndLowerBounds(indexOfSearch);
    }
    ```

***

[[Top]](#top)
