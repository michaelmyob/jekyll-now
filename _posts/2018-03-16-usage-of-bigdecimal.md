---
layout: default
title: "Usage of BigDecimal in Java for accuracy"
---

## Payslip Project

Further progress made on iteration 3 of my payslip project:

#### Usage of _BigDecimal_ in Java for accuracy:
- I refactored all calculations that involved dollar amounts that used _double_ and _int_ primitive types.
- My mentors noted several places in my code where I was performing mathematical operations with those primitive types, and therefore losing accuracy. I was reading about [the reason](https://stackoverflow.com/questions/322749/retain-precision-with-double-in-java/322875#322875) for this, being that these types are binary representations of numbers which aren't truly precise.
- I found a really long, technical, math-heavy monolith of an article that I aim to put here to revisit when I come back to this topic in future, as I'm sure I will: [What Every Computer Scientist Should Know About Floating-Point Arithmetic](https://docs.oracle.com/cd/E19957-01/806-3568/ncg_goldberg.html). There is a better, more modern interpretation here that I've added to my reading list for the coming weeks: [Floating Point Demystified, Part 1](http://blog.reverberate.org/2014/09/what-every-computer-programmer-should.html).

  ```go
  public void calculatePayslips() {
      for (EmployeeRecordInput item : employeeDataInputFromFile) {

          employeeTaxAttributes = new EmployeeTaxAttributes(item.getAnnualSalary(), taxRatesInputData);
          employeeTaxAttributes.calculateTaxRateBoundsBasedOnSalaryInput();

          BigDecimal grossSalaryAmt = item.getAnnualSalary().divide(MONTHS_PER_ANNUM,0,RoundingMode.HALF_UP);

          BigDecimal variableAmountCalculated =  item.getAnnualSalary().subtract(employeeTaxAttributes.getLowerBoundToUseInCalc());
          BigDecimal taxCalculatorComponentA = variableAmountCalculated.multiply(employeeTaxAttributes.getVariableTaxAmountToUseInCalc());
          BigDecimal taxCalculatorComponentB = taxCalculatorComponentA.add(employeeTaxAttributes.getFixedTaxAmountToUseInCalc());
          BigDecimal calculatedTaxAmountTotal = taxCalculatorComponentB.divide(MONTHS_PER_ANNUM, 0,RoundingMode.HALF_UP);

          BigDecimal superPercentage = item.getSuperPercentage().divide(SUPER_PERCENTAGE_DIVISOR,2,RoundingMode.HALF_UP);
          BigDecimal superCalculated = grossSalaryAmt.multiply(superPercentage).setScale(0,RoundingMode.HALF_UP);

          EmployeeRecordOutput output = new EmployeeRecordOutput(
              (item.getFirstName() + " " + item.getLastName()),
              item.getPaymentPeriod(),
              grossSalaryAmt,
              calculatedTaxAmountTotal,
              superCalculated
          );

          employeePayslipDataCalculated.add(output);
      }
  }
  ```

***

[[Top]](#top)
