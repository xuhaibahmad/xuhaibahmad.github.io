---
title: "Parcelize: Boilerplate Free Parcelization With Kotlin"
layout: post
date: 2018-08-20 12:25
image: /assets/images/posts/parcelize.png
headerImage: true
tag:
    - Kotlin
    - Android
category: Blog
blog: true
draft: false
star: true
author: johndoe
description: An easier way of making POJOs parcelable without any boilerplate code.
---

[Serialization](https://en.wikipedia.org/wiki/Serialization
) is the process of converting an object into a stream of bytes for ease of use for different I/O operations. Examples include writing to a database, memory or a file. In context of Android, the most common usecase is to pass an object around Activities and Fragments.

The main idea is to be able to preserve the state of the object in the most primitive way and be able recreated it when needed. The reverse process is called Deserialization.

## Java Serialization & Android

The way our old boy Java used to handle serialization was very simple - You would only need to implement a [marker interface](https://stackoverflow.com/questions/25850328/marker-interfaces-in-java) i.e. `java.io.Serializable` and objects of that class would be automagically serializable.

{% highlight java %}
public class MyObject implements Serializable {
    private int data;
}
{% endhighlight %}

> This looks perfect, let's leave it here and use this awesome interface everywhere. 

But wait! This oversimplification comes at a big cost. The process involves reflection and creation of tonnes of helper objects behind the scenes. Which results in excessive garbage collection and poor performance.

In order to solve this problem, Android came with its own mechanism - [Parcelable](https://developer.android.com/reference/android/os/Parcelable.html). Although it is more robust, it sacrifices the simplicity of `Serialzable` to acheive that. This results in more boilerplate code but which is completely reflection free.

They key to acheiving that, is that we have to explicitly define the serialization process using the `writeToParcel` method. Here's what your ordinary parcelable class with a single field looks like:

{% highlight java %}
public class MyObject implements Parcelable {
    private int data;

     public int describeContents() {
         return 0;
     }

     public void writeToParcel(Parcel out, int flags) {
         out.writeInt(data);
     }

     public static final Parcelable.Creator<MyObject> CREATOR
             = new Parcelable.Creator<MyObject>() {
         
         public MyObject createFromParcel(Parcel in) {
             return new MyObject(in);
         }

         public MyObject[] newArray(int size) {
             return new MyObject[size];
         }
     };
     
     private MyObject(Parcel in) {
         data = in.readInt();
     }
 }
{% endhighlight %}

We have been using this for years now, but if you have been using something like [Parceler](https://github.com/johncarl81/parceler) or if you are used to minimal Kotlin code now, then your reaction would probably be something like:

![Gross!](https://media.giphy.com/media/pVAMI8QYM42n6/giphy.gif#center)

## Kotlin To The Rescue (Again?!)

The release of Kotlin 1.1.4 added experimental support for generating Android Parcelable implementations using the `@Parcelize` annotation. It brings back the simplicity of `Serializable` but without reflection. It instead uses annotation processing to acheive that.

### Setup

Since it is an experimental feature right now, you will need to enable the support for it by adding the following snippet to your `gradle` file:

{% highlight gradle %}
androidExtensions {
    experimental = true
}
{% endhighlight %}

Also, add `apply plugin: ‘kotlin-android-extensions’` to your gradle file if it isn't there already.

### Usage

The process of making any class parcelable involves adding only two things - a `@Parcelize` annotation and marking the class with `Parcelable` implementation.

Here's what our `MyObject` class would look like:

{% highlight kotlin %}
@Parcelize
data class MyObject (val data: Int) : Parcelable
{% endhighlight %}

And that was it! When combined with data classes, we ended up with even simpler code than Java's `Serializable`. 

---

For suggestions and queries, just [contact me](http://linkedin.com/in/xuhaibahmad){:target="_blank"}.
