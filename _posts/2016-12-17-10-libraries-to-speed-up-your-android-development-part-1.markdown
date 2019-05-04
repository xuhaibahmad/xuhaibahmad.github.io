---
title: "Speed Up Your Android Development with these 10 Libraries (Part 1)"
layout: post
date: 2016-12-17 01:08
image: /assets/images/posts/speed-up-android.jpg
headerImage: true
tag:
- Android
- Java
- Development
- Library
- Productivity
category: Blog
blog: true
draft: false
star: true
author: johndoe
description: Speed Up Your Android Development with these 10 Libraries (Part 1)
---

## Introduction

An average developer writes somewhere between few dozen to a couple of hundred lines of code per day. If you are an Android developer, you might be closer to the upper limit. Reason? [Boilerplate](https://en.wikipedia.org/wiki/Boilerplate_code){:target="_blank"}.. **A LOT OF BOILERPLATE!**

From `findViewById()` to writing `Adapters` and `Interfaces` for different types of operation, we write loads of boilerplate everyday (*at least we juniors do!*). Some people still seems surprised about the ***"10 Lines per Developer per Day"*** statement from [The Mythical Man-Month](https://www.amazon.com/Mythical-Man-Month-Software-Engineering-Anniversary/dp/0201835959){:target="_blank"} *(a must read book for any software engineer)* and some even proudly say that they could beat it by huge margin *(impressive!!)*. What many seems to neglect is the importance of *negative code count*. You gain more advantage by removing *(refactoring)* code than by adding new.

> If we wish to count lines of code, we should not regard them as "lines produced" but as "lines spent". - Edsger Dijkstra

Now if you apply this theory to Android development, this makes a big difference. What if you were able to remove even half of the boilerplate code from your projects? You might be able to achieve 2x development speed to say the least. 

Android community is enormous and they keep improving the eco-system with latest libraries and methodologies. There are actually plenty of libraries already written to boost the development speed. In this post, we will discuss my list of 10 *must have* libraries that can boost your Android development.

---

* [ButterKnife](#butterknife)
* [SDP](#sdp)
* [Joda Time](#joda-time)
* [ActiveAndroid](#activeandroid)
* [EventBus](#eventbus)

---

## ButterKnife

> `findViewById()` <br/> `setOnClickListener(this)` <br/>

How many times do you have to write this code in a single project? A year ago, a single `Activity` (with a complex UI) in one of my projects had about 150 lines of only this code (along with some default value settings). Out of embarrassment, I decided to move this code out from `onCreate` method to into its own method `setComponents()` and hide it somewhere near the end of the class. Why? to make it look.. well.. *less embarrassing* \*cough\*

Few months back, I got to work on that project again. First of all, I took a fair share of time to laugh at that mess. I ended up replacing most of the UI related boilerplate with [ButterKnife](http://jakewharton.github.io/butterknife/){:target="_blank"} and the code count of that particular `Activity` came down to somewhere around 900 from 1700+. Image what affect this small changed made to the entire project. 

**Butterknife** is a View Injection library that uses `annotations` to automatically generate common boilerplate code and that too in its own auto-generated classes. This not only saves you a lot of time but also makes your code cleaner and concise. 

### Setup

Using Gradle, add this block to <span class="evidence">build.gradle</span> at the project-level:

{% highlight gradle %}
buildscript {
  repositories {
    mavenCentral()
   }
  dependencies {
    classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8'
  }
}
{% endhighlight %}

And then in your <span class="evidence">app/build.gradle</span> file, add the following:

{% highlight gradle %}
apply plugin: 'android-apt'

...

dependencies {
  compile 'com.jakewharton:butterknife:8.4.0'
  apt 'com.jakewharton:butterknife-compiler:8.4.0'
}
{% endhighlight %}

### Usage

Eliminate `findViewById` with `@BindView` on field declaration:

{% highlight java %}
class ExampleActivity extends Activity {
  // Automatically finds each field by the specified ID.
  @BindView(R.id.title) TextView title;

  @Override public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.simple_activity);
    ButterKnife.bind(this);
    // Start using your fields...
  }
}
{% endhighlight %}

Improved Listeners with `annotations`:

{% highlight java %}
@OnClick(R.id.submit)
public void sayHi(Button button) {
  button.setText("Hello!");
}
{% endhighlight %}

You can even eliminate resource lookups with resource `annotations`:

{% highlight java %}
class ExampleActivity extends Activity {
  @BindString(R.string.title) String title;
  @BindDrawable(R.drawable.graphic) Drawable graphic;
  @BindColor(R.color.red) int red;
  @BindDimen(R.dimen.spacer) Float spacer;
  // ...
}
{% endhighlight %}

This is just the tip of an iceberg, there is a lot more to this library. To learn more, head over to the library's page on creator @JakeWharton 's website.

> **BONUS:** There is even an `Android Studio` plug-in for `Butterknife` called [ButterKnifeZelezny](https://github.com/avast/android-butterknife-zelezny){:target="_blank"}, it automates the binding process with a few clicks, making things lightning fast!

## SDP

No \# 2 on my list is a real underdog but a true life-saver! Even if you have only written a basic [Hello World](https://en.wikipedia.org/wiki/%22Hello,_World!%22_program){:target="_blank"} program so far. You might be well aware of the problems with supporting vast variety of screen sizes associated with Android devices. No matter how many variations you create for your layout, there will always be some devices that will completely destroy your layout.

[SDP](https://github.com/intuit/sdp){:target="_blank"} resolves this problem to some extent by providing a new size unit named `sdp` (scalable dp). This size unit scales with the screen size hence leading to adaptive layouts. Although this approach is not entirely foolproof and most experienced developers might insist on avoiding it, still I found it very helpful and reliable so far. 

### Setup

Add the following dependency in your <span class="evidence">app/build.gradle</span> file:

{% highlight gradle %}
dependencies {
  compile 'com.intuit.sdp:sdp-android:1.0.4'
}
{% endhighlight %}

### Usage

Not much changes with this library. You only need to develop one layout variation for phones and one for tablets *(yes, just two!)*. The process is exactly the same as your normal layout file, the only difference is that you replace `dp` with `sdp` resource values provided by the library in your layout.  

{% highlight xml %}
<View
  android:layout_width="@dimen/_24sdp"
  android:layout_height="wrap_content"
  ...
  />
{% endhighlight %}

To learn more about usage and sample layout, go the project's [GitHub repository](https://github.com/intuit/sdp){:target="_blank"}.

## Joda Time

Almost every project require some level of `date` and `time` manipulation. The standard date and time classes prior to `Java SE 8` are poorly written and broken to some extent. [Joda-Time](http://www.joda.org/joda-time/){:target="_blank"} became the de facto standard date and time library for Java due to its extra ordinary capabilities. The design allows for multiple calendar systems *(such as Gregorian, Julian, Buddhist, Coptic, Ethiopic and Islamic calendar systems)*, while still providing a simple API. There is a lot more to it which you can find out at project's [GitHub repository](https://github.com/JodaOrg/joda-time){:target="_blank"}.

### Setup

Add the following dependency in your <span class="evidence">app/build.gradle</span> file:

{% highlight gradle %}
dependencies {
  compile 'joda-time:joda-time:2.9.6'
}
{% endhighlight %}

### Usage

{% highlight java %}
public boolean isAfterPayDay(DateTime datetime) {
  if (datetime.getMonthOfYear() == 2) {   // February is month 2!!
    return datetime.getDayOfMonth() > 26;
  }
  return datetime.getDayOfMonth() > 28;
}

public Days daysToNewYear(LocalDate fromDate) {
  LocalDate newYear = fromDate.plusYears(1).withDayOfYear(1);
  return Days.daysBetween(fromDate, newYear);
}

public boolean isRentalOverdue(DateTime datetimeRented) {
  Period rentalPeriod = new Period().withDays(2).withHours(12);
  return datetimeRented.plus(rentalPeriod).isBeforeNow();
}

public String getBirthMonthText(LocalDate dateOfBirth) {
  return dateOfBirth.monthOfYear().getAsText(Locale.ENGLISH);
}
{% endhighlight %}

## ActiveAndroid

One of the most painful tasks in Android development is setting up an `SQLite` `Database`. With all the `Keys`, `Tables`, `Cursors`, `Helper` and what not, it usually takes an entire package full of classes (filled with boilerplate code for the most part) to be able to produce even the most simplest Databases. ActiveAndroid takes care of all the setup and messy stuff, and all with just a few simple steps of configuration.

> So what exactly is ActiveAndroid?

ActiveAndroid is an active record style [ORM](https://en.wikipedia.org/wiki/Object-relational_mapping){:target="_blank"} (object relational mapper). Which allows you to save and retrieve SQLite database records without ever writing a single SQL statement. Each database record is wrapped neatly into a class with methods like save() and delete().

### Setup

Add the following dependency in your <span class="evidence">app/build.gradle</span> file:

{% highlight gradle %}
repositories {
  mavenCentral()
  maven { 
    url "https://oss.sonatype.org/content/repositories/snapshots/" 
  }
}

compile 'com.michaelpardo:activeandroid:3.1.0-SNAPSHOT'
{% endhighlight %}

Next, configure your project by adding some global settings in your <span class="evidence">AndroidManifest.xml</span> file:

{% highlight xml %}
<manifest ...>
    <application android:name="com.activeandroid.app.Application"
      ...>

        ...

        <!--Database name-->
        <meta-data android:name="AA_DB_NAME" 
          android:value="Pickrand.db" />
        <!--Database version (default should be 1)-->
        <meta-data android:name="AA_DB_VERSION" 
          android:value="5" />

    </application>
</manifest>
{% endhighlight %}

Notice also that the application name points to the ActiveAndroid application class. If you are using a custom `Application` class, just extend com.activeandroid.app.Application instead of android.app.Application. 

Even if you are already extending `Application` class from another library, you can initialize ActiveAndroid in its `onCreate` method:

{% highlight java %}
public class MyApplication extends SomeLibraryApplication {
    @Override
    public void onCreate() {
        super.onCreate();
        ActiveAndroid.initialize(this);
    }
}
{% endhighlight %}

ActiveAndroid is a very powerful library, and thus its setup process takes a bit longer than others we have seen so far. You might run into few issues in your first attempt as well. For troubleshooting and guides, go to the official [documentation](activeandroid.com){:target="_blank"}.

### Usage

Usage of ActiveAndroid is a very broad topic and even a post dedicated to it will not be sufficient to cover all the features. So we leave this to the official [wiki](https://github.com/pardom/ActiveAndroid/wiki/Getting-started){:target="_blank"} where you can learn almost everything about it.

## EventBus

You might be used to writing `Interfaces` for communication between your Activities and Fragments as well as different modules of your project. Now this is a pretty standard way of doing things but as your projects starts to grow, things may get out of hands.
As a result, developers often end up with [tightly coupling](http://stackoverflow.com/questions/2832017/what-is-the-difference-between-loose-coupling-and-tight-coupling-in-object-orien){:target="_blank"} the components of their Application. This results in a code that is less maintainable and hard to test. 

[EventBus](http://greenrobot.org/eventbus/){:target="_blank"} is a popular open-source library that was created to solve this problem using the [publisher/subscriber pattern](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern){:target="_blank"}.

EventBus enables central communication to decoupled classes with just a few lines of code resulting in simplification of code, low coupling, [high cohesion](http://stackoverflow.com/a/10830225){:target="_blank"}, and ultimately speeding up your development.

### Setup

Add the following dependency in your <span class="evidence">app/build.gradle</span> file:

{% highlight gradle %}
compile 'org.greenrobot:eventbus:3.0.0'
{% endhighlight %}

### Usage

**Define events**: Events are POJO (plain old Java object) without any specific requirements.

{% highlight java %}
public class LoginEvent {
 
    public final boolean status;
 
    public LoginEvent(boolean status) {
        this.status = status;
    }

    public boolean getStatus(){
      return status;
    }
}
{% endhighlight %}

**Prepare subscribers**: Subscribers implement event handling methods that will be called when an event is posted. These are defined with the `@Subscribe` annotation.

{% highlight java %}
// This method will be called when a LoginEvent is posted
@Subscribe
public void handleLoginEvent(LoginEvent event) {
    boolean status = event.getStatus();
    if(status)  // Show welcome message if status is true
      showWelcomeMessage();
    else        // Otherwise show login fail message
      showLoginFailedMessage();
}
{% endhighlight %}

Subscribers also need to register themselves to and unregister from the bus.

{% highlight java %}
@Override
public void onStart() {
    super.onStart();
    // Register subscribers on activity start
    EventBus.getDefault().register(this);
}
 
@Override
public void onStop() {
    // Unregister subscribers on activity stop
    EventBus.getDefault().unregister(this);
    super.onStop();
}
{% endhighlight %}

**Post Events**: Post an event from any part of your code. All currently registered subscribers matching the event type will receive it.

{% highlight java %}
EventBus.getDefault().post(new LoginEvent(isLogin));
{% endhighlight %}

---

These were the first five from the list of libraries to help us speed up our Android development. Find out about the remaining five in the [second part](zuhaibahmad.com/2016-12-18-10-libraries-to-speed-up-your-android-development-part-2){:target="_blank"} of this post.

---

<sub>Most of the example code and definitions were adopted from official sources to avoid any errors.</sub>
