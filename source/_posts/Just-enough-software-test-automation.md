---
title: Just enough software test automation
date: 2018-05-21 18:39:04
tags:
  - 读书笔记
---

Preface
-------

Automated tests are effective only when the test data are designed well.

Automating functional testing requires sophisticated test data that explore the AUT. The test data must reproduce test scenarios that exercise important system features. It requires the test engineer to write a significant portion of the test script as opposed to recording it. It also means designing effective test data. Understanding what to test (having a documented set of test requirements) and designing test data that verify the requirements are the keys to writing valuable automated functional tests.

Understanding how to verify the test results is as important as knowing what to test. Automated test verification is also data dependent.

Chapter 1. What Is Just Enough Test Automation?
-----------------------------------------------

### How Much Automation Is Enough?

To find out how much test automation is enough, we have to look at the areas of the software testing process that can be automated followed by the areas that should be automated.

There is a difference between a tool and a process. Tools are used to facilitate processes.

What are the types of tests that can be automated? They include unit, integration, and system testing. Subcategories of automated system tests include security, configuration, and load testing. Automated regression testing should be implemented at the unit, integration, and system levels during development and for use against major and minor system production releases.

What are the regions of the testing process that we should consider? They include the general spheres of test planning, test design, test construction, test execution, test results capture and analysis, test results verification, and test reporting. There are other activities that are closely associated with the testing activities proper. They include problem (defect) tracking and resolution, software configuration management, and software test metrics. Overall, the activities of the testing process are glued together, as is the software development process, by good project management techniques.

### Testing Process Spheres

#### Test Planning

The traditional idea of a test plan is the who, what, when, where, how, and how long of the testing process.

The point is that we now see the test plan as a working document derived from software requirements and linked to test requirements and test results. It is a dynamic document used during testing. The old idea of test plan is that it is a planning document.

From the test plan scenarios, we create test case requirements. They are constructed directly in scenario tables created in the test plan document, as well as in separate test requirements documents (requirements grids).

#### Test Design

Test design includes identifying test conditions that are based on the previously specified test requirements, developing all possible functional variants of those conditions, divining the expected behavior(s) of each variant when executed against the application under test (AUT), and executing manual tests during the design process prior to test automation.

Designing the tests and the test data is the most time-consuming portion of the testing process. It is also the most important set of activities.

#### Test Reporting

Test reporting is an essential part of the testing process because it documents the test results and their analysis. Two levels of reporting are required—a summary report should be generated for middle- and upper-level technical managers and for customers, and a detailed report should be compiled and presented to the development team members as feedback.

### A Test Automation Group's Scope and Objectives

#### The Scope

A test automation group's purpose should be to develop automated support for testing efforts. This group should be responsible for the design and implementation of a data-driven automated testing framework. They should design and construct suites of automated tests for regression testing purposes.

Chapter 2. Knowing When and What to Automate
--------------------------------------------

### When to Automate System Tests

In general, here are a few practical criteria to use to determine whether or not to automate testing.


1. If the AUT is not complex and not very large, don't automate.
2. If you will receive only a few (three or less) builds, don't automate.
3. If a feature doesn't work at 100%, don't perform automated testing on it, regardless of the size or complexity of the AUT (you can plan for it, but don't create the actual automated test scripts until it is finished and working at 100%).
4. If the development cycle is on a high-speed schedule with short times between deliveries, you simply won't have time to automate it.
5. If a feature cannot be tested with 100% accuracy via automated testing, don't bother unless it saves a considerable amount of manual test time. This does not mean that the feature has to be 100% tested. Remember that software testing almost never provides 100% coverage of each and every feature of an AUT. Don't try to go there. What testing you do perform should never be subjective. The results should always be predictable and should be able to produce a pass or fail condition.


### What to Automate

Your objective should be to automate testing of the critical path(s) of the AUT.

First, automate the primary functions that will be performed by the targeted end-users. Slowly, but surely, add the not-so-critical portions of the application as time permits. Be sure to develop a test coverage matrix showing requirements on one axis and developed tests on the other so that you constantly have a good picture of what has and what has not been automated.

Early in the project do not bother to automate testing of such things as login, user preferences, or other options. Do not automate testing of status bars, help screens, or any other areas of the application that development will pay little attention to until later in the project. Plan for the automation strategy for these items early in the test development phase, but do not implement them until the development effort focuses on them. A bit of common sense tells us that we automate testing of those items that development considers finished or complete.

### Functional Test Data Design

We believe that it is important that test design engineers have a basic knowledge of test case design techniques.

#### Black Box (Requirements-Based) Approaches

**Cause-Effect Graphing**

This approach involves identifying specific causes and effects that are outlined in the requirements document. Causes are conditions that exist in the system and account for specific system behaviors known as effects. Effects can be states that exist temporarily during the processing or system outputs that are the result of the processing. The causes and effects are transformed into a cause-effect diagram that can be used to create test cases.

**Equivalence Partitioning**

This approach uses the system requirements to identify different types of system inputs (input domains). Each input type is defined as an Equivalence Class. Rules are derived to govern each Equivalence Class. The rules are used as a basis for creating test cases.

**Boundary Analysis**

This approach strives to identify Boundary Conditions for each equivalence class. The conditions are used to create test cases containing input values that are on, above, and below the edges of each equivalence class.

**Error Guessing**

This approach uses the tester's experience and intuition to plug any gaps in the test data developed with the other approaches.

#### Hybrid (Gray Box) Approaches

**Decision Logic Tables**

This approach looks at combinations of equivalence classes. It can be used to develop test cases from any portion of the requirements containing complex decision logic structures that would result in If/Then/Else logic in the system processing.

Chapter 4. A Look at the Development of Automated Test Scripts and at the Levels of Test Automation
---------------------------------------------------------------------------------------------------

### Developing Automated Test Scripts

Adhikari cites four test case design principles that are in use at Charles Schwab.  We have applied these rules and found them to be effective when developing automated tests/test data.


1. Design independent test cases.
2. Design self-contained tests.
3. Design home-based test cases.
4. Design nonoverlapping test cases.


No gap and no overlap means that test cases should cover all aspects of all system functions, and that test case redundancy should be eliminated.

Automated tests should


* Test that the application does what it is intended to do (known as either constructive or positive testing)
* Test that the application does not do anything that it is not supposed to do (known as either destructive or negative testing)
* Test that the application is robust (i.e., that it can handle spurious data without crashing); when possible, specific positive and negative results should be identified as expected outputs of specific test scripts.


Fuchs, who prefers writing automated test cases to recording them, offers a threestep approach for creating automated test cases, a discussion of which follows

Step 1. Design the test case.
Step 2. Run the test manually.
Step 3. Automate the test cases. For each test scenario, the test script should contain sections that


* Perform setup
* Perform the test
* Verify the result
* Log the results
* Handle unpredictable situations
* Decide to stop or continue the test case
* Perform cleanup


Test scripts should be organized into test suites that are executed from shell scripts . The MS Visual Test online documentation suggests designing a functional area test suite, a regression test suite, a benchmark test suite, a stress test suite, and an acceptance test suite. We have added three more classes of tests—the unit test suite, the integration/smoke test suite, and the system test suite.

#### Unit-Level Tests


* Unit Test Suite
* Integration/Smoke Test Suite, an automated test suite that is run after the software build is completed.
* Unit-Level Regression Test Suite, tests previously tested features to determine if they continue to function correctly as corrections, tuning, and enhancements are added to the builds during the unit testing phase—these additions can introduce new defects.
* Functional Area Test Suite, should be developed for each functional area you can identify in the AUT.


#### System-Level Tests


* System Test Suite, should perform cross-function tests that are designed to simulate user interactions with the system during normal business processing activities. In addition, the system test suite should include security tests, configuration tests, usability tests, and so forth. As with the functional test, execute the system tests manually the first time. Automate both the functional and system test suites and use them as part of the regression test suite.
* Acceptance Test Suite
* Regression Test Suite, tests previously tested system features to determine if they continue to function correctly as corrections, tuning, and enhancements are added to the system during its maintenance phase—these additions can introduce new defects.


#### Specialized System-Level Tests


* Benchmark Test Suite, tests system performance under varying conditions such as different hardware and software platforms and contrasting system loads. An automated benchmarking tool is an absolute must here.
* Stress Testing Suite, tests the system under extreme conditions such as heavy system loads during peak usage times. These tests can also require an automated testing tool such as Mercury Interactive's LoadRunner or Rational TestManager. There are free performance testing tools available on the WWW—MS Web Analysis Stress Tool, for example. However, tools such as this have limited capacity to develop tests and usually are not sufficient for testing large client-server and Web applications. As is the case with benchmark tests, load and stress tests will not be done if there are no tools because they require too many resources.


Chapter 5. Automated Unit Testing
---------------------------------

TODO

Chapter 6. Automated Integration Testing
----------------------------------------

TODO

Chapter 7. Automated System/Regression Testing Frameworks
---------------------------------------------------------

### The Data-Driven Approach

Data-driven testing is testing where data contained in an input test data file control the flow and actions performed by the automated test script.

The characteristics of data-driven test scripts include the following:


* They use simple input text files.
* They are highly maintainable.
* They are easier for nonprogrammers to use.
* They document the tests that are being executed.
* They allow dynamic data input via placeholders.
* They use input data to control the test execution.


The content of data-driven tests can include (18, 26):


1. Parameters that you can input to the program
2. Sequences of operations or commands that you use to make the program execute
3. Sequences of test data through which the program is driven
4. Placeholders that trigger the test script to create a dynamic data value at runtime
5. Documents that you have the program read and process


Chapter 8. The Control Synchronized Data-Driven Testing Framework in Depth
--------------------------------------------------------------------------

TODO

Chapter 9. Facilitating the Manual Testing Process with Automated Tools
-----------------------------------------------------------------------

### Introduction

A testing group's manual testing activities are usually executed in an ad hoc manner that defeats the repeatability of the tests—for regression purposes, the tests must be executed exactly the same way each time the software is tested/retested.

The software testing process must be repeatable (repeatable means documented; it also means automated).  This indicates that the same test cases are executed in exactly the same way and with the same test data under the same environmental conditions each time the tests are executed. It also indicates that tests must not be susceptible to individual differences among testers because a different person may execute a test each of the times it is run.

Chapter 10. Managing Automated Tests
------------------------------------

TODO
