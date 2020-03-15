---
title: "Painless UI Testing with Kaspresso"
layout: post
date: 2020-03-14 11:00
image: /assets/images/posts/user-interface.png
headerImage: true
tag:
    - Kotlin
    - Android
    - Espresso
    - Instrumentation
    - Testing
category: Blog
blog: true
draft: false
star: true
author: johndoe
description: Guide to writing more intuitive UI tests on Android
---

A few months ago I wrote a blog post under a [similar title](/android-spec-testing/){:target="_blank"} about better Unit Testing for Kotlin with [Kotest](https://github.com/kotest/kotest){:target="_blank"} (formerly KotlinTest). The focus of that post was to demonstrate a more organized, boilerplate free and self-documenting TDD/BDD friendly testing framework for Kotlin.

I have been using Kotest for about a year now and it is safe to say that it has encouraged me to test more of my code. While frameworks like Kotest and Spek provide a good alternative for unit testing, the UI part still feels lacking. Most of us still use Espresso for writing UI tests that, even with its super clean API, feel very repetitive and verbose. For some time, I worked around this by extracting the repetitive code for reuse. It wasn't very clean.

### Caffeine Overflow

Part of my dev-wishlist was to someday establish this perfect loop of TDD flow with Android where all the features start with a failing UI test, followed by multiple unit tests to satisfy the business logic. At the end of which we have a solid well-tested and self-documented feature. So I started searching for other options which lead me to several frameworks, most of which were named after a coffee.

One of the great finds was [Kakao](https://github.com/agoda-com/Kakao){:target="_blank"} by Agoda, it provides a more readable and reusable DSL on top of Espresso. The idea is to encapsulate the UI in `Screen` interfaces which can then be reused. Here is an example of what a form UI would look like:

<script src="https://gist.github.com/71d78d0048162b9fd744387c3dedcee0.js?file=FormScreen.kt"></script>

The resulting tests are reduced to a combination of actions and assertions inside the screen:

<script src="https://gist.github.com/71d78d0048162b9fd744387c3dedcee0.js?file=FormTest.kt"></script>

### But Wait, There's More!

Kakao seemed like a pretty reasonable solution until I stumbled upon [Kaspresso](https://github.com/KasperskyLab/Kaspresso){:target="_blank"}. It combines Espresso and UI Automator into single DSL based API. For the Espresso part, it just wraps around Kakao with some added features such as improved logging, built-in screenshots, ADB command execution, runtime permissions management and more.

---

### Grade Calculator 2.0

What could be a better example than to revisit the grade calculator we built in the previous post? Let's add some UI and this time build in a purely Test-Driven style.

### Installation

Let's create a simple Android project with an empty Activity. Then add the following test dependencies:

<script src="https://gist.github.com/71d78d0048162b9fd744387c3dedcee0.js?file=TestDeps.gradle"></script>

If you set up your project using the Android Studio wizard, then most of these dependencies should already be present. The only dependencies that we must add manually are Kotest, Mockk, and Kaspresso.

Kotest and Mockk are the same as last time for unit testing and mocking, while Kaspresso will be used for UI tests.

### Adding The UI Test

Let's add a UI test for the happy path of our calculator without touching the activity first:

<script src="https://gist.github.com/71d78d0048162b9fd744387c3dedcee0.js?file=GradeCalculatorUiTestStep1.kt"></script>

If we try to run this test, it will fail because we have not added the `GradeCalculatorScreen` yet. This test serves as a blueprint of what we want to achieve for the happy path of grade calculation. Let's break down each step:

- **TestCase** - The first thing you might have noticed is that the test class extends `TestCase`, which is a base class provided by Kaspresso that gives access to most of its features.
- **Activity Test Rule** - Next, we create an activity test rule for the grade activity. This part is the same as normal Instrumentation tests.
- **GradeCalculatorScreen Instance** - This instance of the screen will be used in the Kaspresso DSL.
- **Test Body** - For the test body, you might notice the chain of 3 separate blocks. You can use the block syntax instead of the expression body, but then it will add an extra indent to all the code inside the block which I do not like very much.
- **Before-After-Run** - The `before` and `after` blocks are interceptors for anything you might want to do before or after the test respectively. For example, `before` can be used for mocking and `after` can be used to release resources.
- **Steps** - Finally, the run block is where actual testing happens
  - Every step is described with a `step` block. This part is optional, you can skip the steps if you want and directly write the encapsulating code. However, it is very helpful since not only it provides a comment on what is happening but this information also gets printed in the logs.
  - In the first step, we launch the activity, this can be done directly with the test rule though.
  - In the next two steps, we perform some actions on the `GradeCalculatorScreen` and then verify their results. Note that all the methods come from the library, we only provide view bindings in the `Screen` implementations.

### Implementing The Kaspresso Screen

At this point, we cannot even compile the application due to missing classes. Let's provide an implementation for our `GradeCalculatorScreen`.

<script src="https://gist.github.com/71d78d0048162b9fd744387c3dedcee0.js?file=GradeCalculatorScreen.kt"></script>

As explained earlier, the screen class just binds the UI elements and organizes them into one place. Since we do not have these elements in the layout XML, the project won't compile unless we add them too. So here's a simple layout for it:

<script src="https://gist.github.com/71d78d0048162b9fd744387c3dedcee0.js?file=activity_grade_calculator.xml"></script>

Now, the test will compile but the test will fail because we have not provided any business logic yet.

### Adding The Business Logic

The next step is to provide the business logic to calculate the grade.

> Since there is nothing new about this part, I will just copy the tests and resulting logic from the previous post. You can read the [Kotest post](/android-spec-testing/){:target="_blank"} to understand better.

Create a `GradeCalculatorSpec` class inside the test package and add the specs for grade calculation:

<script src="https://gist.github.com/71d78d0048162b9fd744387c3dedcee0.js?file=GradeCalculatorSpec.kt"></script>

Once all the tests are passing, the resulting implementation would look something like this:

<script src="https://gist.github.com/71d78d0048162b9fd744387c3dedcee0.js?file=GradeCalculator.kt"></script>

### Binding It All Together

The final step is to bind the `GradeCalculator` with the UI. Let's add a click listener to the button and use the user input to calculate the grade. 

<script src="https://gist.github.com/71d78d0048162b9fd744387c3dedcee0.js?file=GradeCalculatorActivity.kt"></script>

If all goes well, all tests should be passing now. Also, if you look at the logcat output, it will have a complete summary of all test steps.

> Bonus: If there was no issue with permission, it should have also captured the screenshots for each step automagically! Look inside the gallery for them.

Here's the pidcat output of my tests:

<script src="https://gist.github.com/71d78d0048162b9fd744387c3dedcee0.js?file=TestOutput.txt"></script>

Using Kaspresso, you can also handle runtime permissions, change device state, execute ADB commands right from your code. The flexibility it provides for writing tests in truly amazing.

---

### Conclusion

This is a very basic example that does not handle any edge cases. However, I hope it was enough to give you a taste of test-driven development with Android.

We started with a crude idea without any implementation details in mind and used tests to shape our logic. There are a couple of benefits to this approach:

- We ended up with a minimum workable solution with 100% code coverage
- As every part of our logic is backed by Unit and UI tests, the tests themselves serve as a documentation for the features.
- Since both the architecture and the logic is shaped by the tests themselves, it is quite easy to freely refactor the code further without worrying about regression bugs.

---

You can find complete source code for this post [here](https://github.com/xuhaibahmad/Kaspresso-Testing-Demo){:target="_blank"}

For suggestions and queries, just [contact me](http://linkedin.com/in/xuhaibahmad){:target="\_blank"}.
