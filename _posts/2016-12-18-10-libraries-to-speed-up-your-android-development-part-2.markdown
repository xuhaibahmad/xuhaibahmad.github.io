---
title: "Speed Up Your Android Development with these 10 Libraries (Part 2)"
layout: post
date: 2016-12-18 02:42
image: /assets/images/posts/speed-up-android.jpg
headerImage: true
tag:
- Android
- Java
- Development
- Library
- Productivity
blog: true
draft: true
star: true
author: johndoe
description: Speed Up Your Android Development with these 10 Libraries (Part 2)
---

In the [first part of this post](zuhaibahmad.com/10-libraries-to-speed-up-your-android-development-part-1){:target="_blank"}, we learnt about [ButterKnife](zuhaibahmad.com/10-libraries-to-speed-up-your-android-development-part-1/#butterknife){:target="_blank"}, [SDP](zuhaibahmad.com/10-libraries-to-speed-up-your-android-development-part-1/#sdp){:target="_blank"}, [Joda Time](zuhaibahmad.com/10-libraries-to-speed-up-your-android-development-part-1/#joda-time){:target="_blank"}, [ActiveAndroid](zuhaibahmad.com/10-libraries-to-speed-up-your-android-development-part-1/#activeandroid){:target="_blank"} and [EventBus](zuhaibahmad.com/10-libraries-to-speed-up-your-android-development-part-1/#eventbus){:target="_blank"} libraries to help us speed up our Android development. Now, let's continue from where we left off and explore the remaining libraries in our list.

---

* [Volley](#volley)
* [Retrofit](#retrofit)
* [Parceler](#parceler)
* [Android Annotations](#android-annotations)
* [Espresso](#espresso)

---

## Volley

Network operations have always been a frustrating task for developers mainly due to the excessive amount of tedious work involved. Without the help of any networking library, to read from even the most basic REST APIs that spits data in `JSON` format, you will need to setup an `HttpURLConnection`, `InputStream`/`OutputStream`, a *JSON parser* and you will also have to place all these tasks into an `AsyncTask` to separate them from UI thread and run asynchronously. That's a lot of work with a lot of boilerplate code, probably more than anything else considering the work we are getting done for the amount of code that we write.



### Setup

### Usage

## Retrofit

### Setup

### Usage

## Parceler

### Setup

### Usage

## Android Annotations

### Setup

### Usage

## Espresso

So far we have covered different aspects of our development routine and tried to simplify them with the use of 3rd party libraries. This seems to increase overall performance and productivity in our projects but in the end if you cannot test it, it's probably not worth it. [Testing](){:target="_blank"} has been an integral part of Software Engineering and Android development is no different. In your early days of Android development, you might be testing your applications manually by test driving them every time you added or fixed something. Many might still be doing the same if you have not switched to automated tools yet.

[Espresso](){:target="_blank"} is part of [Android Testing Support Library](){:target="_blank"} provided by [Google](){:target="_blank"}. The basic idea is to get rid of manual testing and let the system do the UI testing for you in matter of seconds. You write intuitive and easy to read tests in the form of each individual action you have to perform in order to get the task done and then finally evaluate the results. All the operations in Espresso follow this simple pattern

> **Find A View** ➜ **Perform Some Action(s)** ➜ **Validate Output**

Each of the steps in your test path are more or less exactly written step by step in Espresso and even a person with little or no knowledge about the domain or your project can easily start writing tests for your application. This ease of use makes it one of the best (if not ***the best**) UI testing framework available for Android.

### Setup

### Usage

If you are to test the Login screen of your application, you might have to first input email and password in the corresponding fields and then click the Sign In/Log In button. These are 3 separate operations, additionally you might want to first check if Login Form itself is visible. Finally you will check if the application displays a dialog indicating login success in case of successful Login. Let's see how to do this using Espresso.

{% highlight gradle %}
@RunWith(AndroidJUnit4.class)
public class LoginActivityTest {

    @Test
    public void onLoginSuccess_displaysLoginSuccessDialog() {
    	//Declare our email and password
    	String username = "myusername@gmail.com";
    	String password = "mypassword";

    	// Login success dialog's message
    	String successMsg = "Login Successful";

        // Check if Login form is visible
        onView(withId(R.id.LLLogin)).check(matches(isDisplayed()));
        // Fill details in form
        onView(withId(R.id.ETUsername)).perform(typeText(username));
        onView(withId(R.id.ETPassword)).perform(typeText(password));
        // Click on Login button
        onView(withId(R.id.BTLogin)).perform(click());
        // Check if Login Success dialog is displayed
        onView(withText(successMsg)).check(matches(isDisplayed()));
    }
}
{% endhighlight %}

As you can see, this is just an *easy to read and easy to write* code representation of a real user's actions. You can create all sorts of complex tests by chaining these simple methods together. There are many different types of `actions`, `matchers` and other utilities available in [Espresso API](){:target="_blank"}, to learn more about them, head over to the official [Espresso Documentation](){:target="_blank"}.

---

## Conclusion

So these were the ten must have libraries in my opinion that every Android developer must start using in order to make their code:

* Easy to write
* Easy to test
* Easy to maintain
* Concise and Performant
* Highly cohesive and low coupled

and finally improving your overall development speed and productivity, because in the end being a good developer is about *working smart, not hard*. I hope you have found this article useful. Feel free to provide your valuable feedback.

---

*<sub>Most of the example code and definitions were adopted from official sources to avoid any errors.</sub>*