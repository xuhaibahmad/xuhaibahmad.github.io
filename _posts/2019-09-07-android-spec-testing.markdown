---
title: "Painless Unit Testing with Kotlintest & Mockk"
layout: post
date: 2019-09-07 11:00
image: /assets/images/posts/chemistry.png
headerImage: true
tag:
    - Kotlin
    - Android
    - Unit Testing
    - Spec
category: Blog
blog: true
draft: false
star: true
author: johndoe
description: Guide to writing more intuitive and enjoyable unit tests on Android
---

Testing is hard and not everyone likes to spend their time writing unit tests when they could be building shiny new features.

While almost every developer understands the value of testing their code, most of us fall prey of laziness in the face of approaching deadlines and prospect of more exciting work.

In this post, let's understand why unit tests serve as the backbone of successful products and learn a new way of writing tests that is much simpler, intuitive and appealing.

---

## Why Testing Matters

Since I mostly work with Android applications. I'll let you in on a little secret. More than half of the code bases you will come across will have little to no tests. Not just the crappy ones, but sometimes products with thousands of users.

This is often due to the complex and dull nature of old school [JUnit](https://junit.org) based testing. Not only the process is quite boring, it also requires a lot of repetitive boilerplate code to be written for even the most simplest scenarios, making testing a very time consuming process.

Obviously, when you are in a rush to take the product out as soon as possible, testing becomes least desirable practice.

More often than not, the end result is that project becomes unmaintainable within a year. I have seen organizations rewriting already published products only because the code base has become unmaintainable due to spaghetti code that is rotten with bugs.

Obviously this is not a desirable situation. Testing your code for bugs and architectural flaws from the beginning solidifies your product to be manageable in the longer run.

> "If you can not measure it, you can not improve it." - Lord Kelvin

## Testing As You Go

Laying the foundations of your project with extensive testing is not a new idea. [Behavior Driven Development](https://en.wikipedia.org/wiki/Behavior-driven_development) has been around for decades.

The practice is to turns feature specifications (or user stories) into a set of unit tests and then writing code that satisfies those specs via passing tests.

This results in co-development of both your features and their tests side by side. Coupled with a Continuous Integration solution, this setup almost guarantees that you will never have any regression bugs.

---

## Specification Based Testing In Practice

[Cucumber](https://cucumber.io/) is the most widely used BDD testing framework right now. It provides a plain language parser called Gherkin which can be used to write tests in plain English that non-programmers can also understand. The framework turns these specifications into acceptance tests which also serves as the documentation for each feature. Here is what a test written in Gherkin looks like:

{% highlight cucumber %}
Feature: Calculator
  As a user
  I want to use a calculator to add numbers
  So that I don't need to add myself
 
  Scenario: Add two numbers -2 & 3
    Given I have a calculator
    When I add -2 and 3
    Then the result should be 1
    
  Scenario: Add two numbers 10 & 15
    Given I have a calculator
    When I add 10 and 15
    Then the result should be 25
{% endhighlight %}

Neat! Isn't it? This spec results in unit tests that run with Cucumber. Since this post is not about Cucumber, I won't go into the implementation details. You can visit this [tutorial](https://www.baeldung.com/cucumber-scenario-outline){:target="\_blank"} for full tutorial on BDD style testing with Cucumber. I used the same article to borrow the above example.

Some other popular BDD frameworks include [Lettuce](http://lettuce.it/){:target="\_blank"}, [Jasmine](http://jasmine.github.io){:target="\_blank"}, [SpecFlow](http://www.specflow.org/){:target="\_blank"}, [Spek](https://spekframework.org/){:target="\_blank"}, [Behat](http://behat.org){:target="\_blank"}, [Jdave](https://github.com/jdave/Jdave){:target="\_blank"} and [Jbehave](http://jbehave.org/){:target="\_blank"}

---

## Problems with JUnit

JUnit is a industry standard at this point for all things Java. It has ruled the Java development for decades now and it works. But since it was built for Java, it brings with it most of the common pitfalls of Java based technologies that feel backwards, specially in the world of modern programming languages like Kotlin, JavaScript and Python.

Having said that, writing all styles of tests are entirely possible with JUnit. [Mockito](https://site.mockito.org) provides a BDD style extension in its core library that allows you to do similar `Give, When, Then` style testing within JUnit test cases.

Let's take a look at some of the problems associated with it and why I believe vanilla JUnit is not a good combination for a powerful language like Kotlin:

### Code Repetition

Like everything in Java, JUnit is also quite verbose. This results in too much repetition of similar contents with slight change of logic to test different scenarios.

How many time have you had a test for a HAS and a HAS-NOT condition with all the same contents except for a boolean? For example, a `testUserAccessWhenHasToken` and then `testUserAccessWhenNotHasToken`.

Of course, you can stuff in all your assertions in one test at the cost of readability. However, a good test is supposed to be granular i.e. targeting one case per test.

Also, your test code is guaranteed to grow as your project gets bigger and at some point your test code will surpass your application code. If you want to look at examples of this, try looking into the source code of any popular open source projects such as [RxJava](https://github.com/ReactiveX/RxJava), [Retrofit](https://square.github.io/retrofit/), [OkHttp](https://square.github.io/okhttp/), [Picasso](https://square.github.io/picasso/) etc. Almost all of them have 1.5x to 2x more test code compared to their business logic.

### Lack Of Contextual Information

If you are like me, then one of the first things that you may have done while switching to Kotlin was to change your long JUnit test names like `fun onTouchOutside_shouldDismissDialogAndResumeStreaming()` with back tick notation. While a big improvement, it gave us a license to go wild with it in our attempts to provide more contextual information about each test case. So now our test case has evolved into something like this: ``fun `on touch outside, dismiss dialog and resume streaming the paused song` ()``

The real issue here is that these names are not enough to provide full context about the test. Unless you are willing to write entire paragraph to define the pre and post conditions for the scenario.

Another problem here is with the organization of test cases. Most of the test cases have some shared code that can be logically structured into a hierarchy. However, because JUnit only allows you to write tests in the form of class methods. So you end up writing more tests, more code but with less context about overall theme of the current group of tests.

---

## A Better Way To Write Tests

The reason behind giving you a taste of Cucumber BDD was to show what it would be like to have such idiomatic tests.

Let's define a set of specification that we want to build and test. We will be using two libraries [Kotlintest](https://github.com/kotlintest/kotlintest) and [Mockk](https://mockk.io/) to help us write tests.

### Feature

Let's build a grade calculator that tells you your grade based on the marks you obtained. Here are the rules for grading:

- When obtained marks are 90 or above, then grade is A.
- When obtained marks are between 80 and 89, then grade is B
- When obtained marks are between 70 and 79, then grade is C
- When obtained marks are between 60 and 69, then grade is D
- When obtained marks are below 60, then grade is F

### Installation

First, create an empty Kotlin or Android project and add the following two dependencies:

{% highlight groovy %}
testImplementation 'io.kotlintest:kotlintest-runner-junit5:3.3.2'
testImplementation 'io.mockk:mockk:1.9.3.kotlin12'
{% endhighlight %}

The first dependency is Kotlintest which is a testing library built on top of JUnit. It takes advantage of Kotlin's DSL capabilities to support various type of testing styles.

Mockk is a Kotlin-based mocking library with a very clean syntax that blends really well with Kotlintest's specs. It also provides much more flexible API and much wider set of feature compared to Mockito or Powermock.

### Implementation 

Let's start by creating an empty `GradeCalculator` class and converting our specifications into a Spec.

{% highlight kotlin %}
class GradeCalculatorSpec : BehaviorSpec({

    Given("a grade calculator") {
        val calculator = spyk(GradeCalculator())

        every { calculator.totalMarks } returns 100
        val total = calculator.totalMarks

        When("obtained marks are 90 or above") {
            Then("grade is A") {}
        }
        When("obtained marks are between 80 and 89") {
            Then("grade is B") {}
        }
        When("obtained marks are between 70 and 79") {
            Then("grade is C") {}
        }
        When("obtained marks are between 60 and 69") {
            Then("grade is D") {}
        }
        When("obtained marks are below 60") {
            Then("grade is F") {}
        }
    }
})
{% endhighlight %}

This does not quite look like our traditional JUnit test. So let's break it down and understand bit by bit:

- **BehaviorSpec** - We are extending something called a *BehaviorSpec* which is basically a Spec written in BDD style (remember the Give, When, Then from Cucumber?). There are dozens of different other Spec styles available in Kotlintest.
- **Spyk** - You may notice that I wrapped the `GradeCalculator` object with a `spyk` method. If you have used Mockito before than the concept is the same. Basically, a spy is a wrapper that lets you mock some methods and variables of the object while using the actual values for the rest. I did it to demonstrate the use of Mockk here.
- **Every/Returns** - Similar to Mockito's `when/then` style, this construct is used by Mockk to to prepare mock values. All we are saying is to return a mock value whenever anyone in this code block asks for this value. The value we are mocking is total marks which are going to help calculate the grade.
- **When/Then** - Finally, there are a bunch of `Then` blocks nested inside `When` blocks with some description. The `When` block is nothing but a way to organize tests in a logical fashion while each `Then` serves as the actual test where the assertion happens. So you can have a test with just a `Then` statement but then there's no point of using it.

Next, lets add some functionality to the `GradeCalculator` class.

{% highlight kotlin %}
class GradeCalculator {

    var totalMarks = 0

    fun getGrade(obtainedMarks: Int, totalMarks: Int): String {
        val percentage = getPercentage(obtainedMarks, totalMarks)
        return when {
            percentage >= 90 -> "A"
            percentage in 80..89 -> "B"
            percentage in 70..79 -> "C"
            percentage in 60..69 -> "D"
            else -> "F"
        }
    }

    private fun getPercentage(obtainedMarks: Int, totalMarks: Int): Int {
        return  (obtainedMarks / totalMarks.toFloat() * 100).roundToInt()
    }
}
{% endhighlight %}

Here I added a `totalMarks` field which we mock in our test. This value is used in the `getPercentage` method to calculate the percentage between total and obtained marks.

Finally, the `getGrade` calculates the grade by comparing the calculated percentage with different ranges.

You can build this class in a TDD fashion by running the tests first and adding the functionality to make the failing tests pass one by one. I believe the end result would still be somewhat similar.

In the end, lets add some assertions to test the specifications we just wrote. The final implementation would look something like this:

{% highlight kotlin %}
package com.zuhaibahmad.bddtestingtutorial

import io.kotlintest.shouldBe
import io.kotlintest.specs.BehaviorSpec
import io.mockk.every
import io.mockk.spyk

class GradeCalculatorSpec : BehaviorSpec({

    Given("a grade calculator") {
        val calculator = spyk(GradeCalculator())

        every { calculator.totalMarks } returns 100
        val total = calculator.totalMarks

        When("obtained marks are 90 or above") {
            val grade = calculator.getGrade(93, total)

            Then("grade is A") {
                grade.shouldBe("A")
            }
        }
        When("obtained marks are between 80 and 89") {
            val grade = calculator.getGrade(88, total)

            Then("grade is B") {
                grade.shouldBe("B")
            }
        }
        When("obtained marks are between 70 and 79") {
            val grade = calculator.getGrade(78, total)

            Then("grade is C") {
                grade.shouldBe("C")
            }
        }
        When("obtained marks are between 60 and 69") {
            val grade = calculator.getGrade(68, total)

            Then("grade is D") {
                grade.shouldBe("D")
            }
        }
        When("obtained marks are below 60") {
            val grade = calculator.getGrade(59, total)
            Then("grade is F") {
                grade.shouldBe("F")
            }
        }
    }
})
{% endhighlight %}

### Conclusion

I discussed above that JUnit tests lack contextual information and proper grouping of co-related tests. You can see how every test in the spec has a hierarchy which can be used to compose complex cases.

Furthermore, for this particular style of testing, kotlintest provides an additional `And` block to allow you to create even more complex tests without loosing contextual information. For example, you may want to construct a test like this:

{% highlight kotlin %}
When("user list is fetched from API") {
    And("the internet is NOT available"){
        Then("Test something"){
            // Some assertions
        }
    }
    And("the internet is available"){
        Then("Test something"){
            // Some assertions
        }
    }
}
{% endhighlight %}

Finally, not just the tests are well organized, the test results in Android Studio have that nested style too. Here's the result of our tests in the IDE:

![Spec Test Result](../assets/images/posts/spec-test-results.png)

---

You can find complete source code for this post [here](https://github.com/xuhaibahmad/Spec-Testing-Demo){:target="_blank"}

For suggestions and queries, just [contact me](http://linkedin.com/in/xuhaibahmad){:target="\_blank"}.
