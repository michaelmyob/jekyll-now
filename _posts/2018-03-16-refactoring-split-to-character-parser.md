---
layout: default
title: "Refactoring split (regex) to a character parser"
tags: project_learnings payslip_project
---

## Payslip Project

Further progress was made on iteration 3 of my payslip project.

#### Refactoring the use of 'split' into a character parser
- Background:
  - Within my payslip project, there is some string manipulation required within the input file - in particular, the input file prescribes that the percentage amount for superannuation is written as "x%", with x being represented as an integer on the specification, but it could also be a floating style value. The important factor here is that a percentage symbol is included.
  - To extract the percentage symbol away, and just retrieve the numerical value, I initially used the __.split__ method within Java's _String_ class.
  - Based on mentor feedback, this could present a dangerous situation if the user input is not exactly following the prescribed input format. There is some debate about [this online, too](https://stackoverflow.com/questions/998997/should-i-avoid-regular-expressions).
- Solution:
  - For safety, I refactored the functionality of this block to act more as a character parser, just taking characters that are _only_ digits, specifically, and discarding the rest.
  - This way, even if other characters are written in that data element, it's still able to find digits and use those for it's super calculation.

    ```go
    private String extractNumericalValuesFromSuperPercentage(String superValueInput) {

      StringBuilder stringBuilder = new StringBuilder();

      for (char charInSuperStringInputted : superValueInput.toCharArray()) {
          if (Character.isDigit(charInSuperStringInputted)) {
              stringBuilder.append(charInSuperStringInputted);
          }
      }
      return stringBuilder.toString();
    }
    ```

***

[[Top]](#top)
