---
title: "Pythonic Imports in Kotlin"
layout: post
date: 2018-12-1 16:00
image: /assets/images/posts/import.png
headerImage: true
tag:
- Kotlin
- Android
blog: true
draft: false
star: true
author: johndoe
description: Improved imports with Kotlin.
---

If you have been working with Kotlin for some time now, you might have encountered issues with name conflicts with Kotlin's reserved keywords. A common example is with the popular mocking library [Mockito](https://site.mockito.org/), where the `when` method  which is now used by Kotlin for its switch-like construct. So you end up doing something like this:

{% highlight kotlin %}
`when`(messenger.getMessage()).thenReturn("Hello World!")
{% endhighlight %}

Another scenario is when you want to name the imported class or extension function something more meaningful and readable. Which can be acheived to some extent with the use of a `typealias`. However, there's a better solution available right into the Kotlin standard library.

## Pythonic Way of Handling Imports

One the most flexible things I find in `Python` is the ability to name the imported classes almost anything you want, just like a variable. So you would simply import a class with a name that suits your style e.g.

{% highlight python %}
import matplotlib.pyplot as plt
{% endhighlight %}

Now, you can use `plt` as a variable throughout your script.

# Kotlin's Import-As Alias

I wished to have this for Android development after I ran into some ambiguity issues with a recent project. Upon some searching, I was surprized to find that Kotlin had this feature all along. Just like Python, you can simply add imports with an `as` keyword.

Let's look at how our Mockito problem is resolved now:

{% highlight python %}
import org.mockito.Mockito.`when` as whenever
{% endhighlight %}

You can now use `whenever` in place of the less pleasant `'when'` througout the class.

{% highlight kotlin %}
whenever(messenger.getMessage()).thenReturn("Hello World!")
{% endhighlight %}

So that was it. Another reason to love Kotlin (and Python as well)! ;)

---

For suggestions and queries, just [contact me](http://linkedin.com/in/xuhaibahmad){:target="_blank"}.
