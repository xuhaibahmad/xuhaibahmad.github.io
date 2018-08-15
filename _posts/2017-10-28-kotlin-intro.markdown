---
title: "Kotlin: Java on steroids or something more?"
layout: post
date: 2017-10-28 01:10
image: /assets/images/posts/kotlin_vs_java.png
headerImage: true
tag:
- Kotlin
- Java
- Android
- Productivity
blog: true
draft: false
star: true
author: johndoe
description: An overview of Kotlin language, features that makes it different from Java and comparison with its ancestor.
---

The first time I heard about Kotlin was back in 2015 and my initial reaction was like *"Meh! Just another heck into Android ecosystem that would lead to disasters. I'll pass!".* I waited and waited for the hype to die down -- but it never did. 

The popularity of Kotlin kept on increasing. There was something about Kotlin, there was something that's lacking in Java, I just didn't knew what it was and being a Java fanboy I never bothered investigating. Whatever the case was, everyone was going crazy over it. So finally the prayers were answered and Google made [Kotlin an Official language for Android](https://developer.android.com/kotlin/index.html){:target="_blank"} at the [Google I/O '17](https://events.google.com/io/){:target="_blank"}.

## Background

The reason for my immediate disapproval was because of the huge number of half-baked platforms and tools emerging for Android during that period. Every single one of them was following the same pattern. They all started out with a bang, did some impressive demos, showed great promise, made it look like everything is going to change, eventually hit technical barriers and then disappeared forever or almost died.

They were hyped not because there was something groundbreaking about them but because there were actual problems with Java and people were desperately looking for alternatives. Anyone coming from Python, Swift or Javascript background could see the huge gap between Java and other modern programming languages.

While there were *lambdas*, *optional types*, easier *asynchronous processing* mechanisms and loads of other useful features in others; Java developers were still fighting *Null Pointer Exceptions* and writing tons of boilerplate crap everyday.

## Kotlin To The Rescue

Right after the acceptance from Google and built-in support for it in Android Studio 3, I started taking interest in it. Boy I was wrong about Kotlin! Just this single [demo by Hadi Hariri](https://www.youtube.com/watch?v=X1RVYt2QKQE){:target="_blank"} done at the Google I/O was enough to convert me. Learning curve is lean for anyone familiar with Java so it took roughly 2 days to grasp the core concepts and starting to write first production application. 

Now I was having a good time with it (already a fan btw!), the codebase was very concise, boilerplate free and readable - in fact pretty much self-explanatory. However, still I couldn't come up with an expression to explain that magic. Sure there were thousands of technical arguments in its favor but it was not something you'd tell your manager when trying to convince them to use it. Then recently while watching [this talk on RxJS](https://www.youtube.com/watch?v=gT6il5fJyAs){:target="_blank"} it clicked! 

> Programming should be more about "what".

One of the reasons why Python is so popular in scientific and research domains is because it is doing exactly that. It enables them to focus on solving problems instead of tangling into platform specific low level details. 

On the other hand we are still focusing too much on 'How' rather than 'What' to do. A developer should be concerned with completing the task at hand with few lines of code instead of fighting the issues and barriers put in their path by the platform itself.

Alright, enough ranting about Java! Let's take a look at what Kotlin has to offer that makes it different.

## Bringing Good Of All Worlds

After having a taste of Python, Javascript and a little bit of Swift, it was easy to identify the source of inspiration for many features (Yes, I know Kotlin is older than Swift and they didn't copy). However, even after using it for a few months, I believe there are still a lot of undiscovered bits that are yet to be explored. So for brevity's sake, I'll be focusing only on the major features.

### Type Safety

The selling point for [JetBrains](https://www.jetbrains.com){:target="_blank"} has been **"NPE Free Language"** for the most part and why wouldn't it be? We're all familiar with null pointer exception jokes made on Java developers. 

Specially on Android it is quite annoying when the entire application just blows up in your face due to a sudden NPE and then as a developer you'll have to spend a lot of time going back to the same state where you were before. 

> **.. and GOD Help you if they show up in the production code.**

In Kotlin, the syntax is different for references that can hold `null` and those that can not. For example, a regular variable of type `String` can not hold `null`:

{% highlight kotlin %}
var a: String = "abc"
a = null    // This leads to compile time error 
{% endhighlight %}

The syntax to allow `null`s is to add a trailing question mark after the type declaration e.g. `String?`:

{% highlight kotlin %}
var b: String? = "abc"
b = null    // Acceptable
{% endhighlight %}

Now, if you call a method or access a property on `a`, it's guaranteed not to cause an NPE, so you can safely say:

{% highlight kotlin %}
val l = a.length
{% endhighlight %}

But if you want to access the same property on `b`, that would not be safe, and the compiler reports an error:

{% highlight kotlin %}
val l = b.length // error: variable 'b' can be null
{% endhighlight %}

It doesn't stop at this, type safe calls allows you to eliminate `if (someValue != null)` blocks for nullable types with simple `?.` operator which essentially means *execute this only when the value is not `null`*. 

{% highlight kotlin %}
val listWithNulls: List<String?> = listOf("A", null)
for (item in listWithNulls) {
     item?.let { println(it) } // prints A and ignores null
}
{% endhighlight %}

You can also apply an alternate logic for `null` case using **elvis** operator which is equivalent to the `else` block that follows the *if not null* block. The syntax for it is `?:`, so your conditional code will look something like this:

{% highlight kotlin %}
val l = b?.length ?: -1
{% endhighlight %}

This is just an overview, it'll take at least an entire post to cover all of the type system. You can read more about it on the [Kotlin's official documentation](https://kotlinlang.org/docs/){:target="_blank"}

### Data Classes

This one is my personal favorite so far. How much code do you have to write for a data class? And how much more do you have to add if you need to add `equals()`, `hashCode()`, `clone()` and `toString()` functionality? This is your average data class declaring fields and providing that additional functionality along with getters and setters:

{% highlight java %}
public class Customer {
   private String name;
   private String email;
   private String company;

   public Customer(String name) {
       this(name, "", "");
   }

   public Customer(String name, String email) {
       this(name, email, "");

   }

   public Customer(String name, String email, String company) {
       this.name = name;
       this.email = email;
       this.company = company;
   }

   public String getName() {
       return name;
   }

   public void setName(String name) {
       this.name = name;
   }

   public String getEmail() {
       return email;
   }

   public void setEmail(String email) {
       this.email = email;
   }

   public String getCompany() {
       return company;
   }

   public void setCompany(String company) {
       this.company = company;
   }

   @Override
   public boolean equals(Object o) {
       if (this == o) return true;
       if (o == null || getClass() != o.getClass()) return false;

       Customer c = (Customer) o;

       if (name != null ? !name.equals(c.name) : c.name != null) 
            return false;
       if (email != null ? !email.equals(c.email) : c.email != null) 
            return false;
       return company != null 
            ? company.equals(c.company) 
            : c.company == null;
   }

   @Override
   public int hashCode() {
       int result = name != null ? name.hashCode() : 0;
       result = 31 * result + (email != null ? email.hashCode() : 0);
       result = 31 * result + (company != null ? company.hashCode() : 0);
       return result;
   }

   @Override
   public String toString() {
       return "Customer{" +
               "name='" + name + '\'' +
               ", email='" + email + '\'' +
               ", company='" + company + '\'' +
               '}';
   }
}
{% endhighlight %}

**That's around 70 lines for a simple POJO!** Even if the IDE generates them for us, it is every difficult to maintain in the longer run. Now let's look at what the Kotlin equivalent of this would look like:

{% highlight kotlin %}
data class Customer(var name: String, var email: String = "", var company: String = "")
{% endhighlight %}

Yes, that's it! A single line that provide you a fully functional data object with  `equals()`, `hashCode()`, `clone()` and `toString()` functionality baked right into it. So now your entire package of data classes can be combined into a single Kotlin file.

### More Intuitive Flow Control

> There are `if-else` statements and there are `switch` statements, then there are Python-like `if-else` constructs and then there's Kotlin's `when` construct. 

A `when` improves the traditional `switch` operator with more readable syntax and increased flexibility for different cases. It can be used both as a statement and as an expression. Following is an example:

{% highlight kotlin %}
when (x) {
    in 1..10 -> print("x is in the range")
    in validNumbers -> print("x is valid")
    !in 10..20 -> print("x is outside the range")
    is String -> print("x is not a number!")
    else -> print("wtf are you feeding me?")
}
{% endhighlight %}

On a side note, just like the `when` operator, the `if-else` statements also return values in Kotlin.

For looping, we can use `in` operator instead of less meaningful `:` from Java:

{% highlight kotlin %}
for (item in array) {
    print(item)
}
{% endhighlight %}

Alternatively, you can use the withIndex library function:

{% highlight kotlin %}
for ((index, value) in array.withIndex()) {
    println("the element at $index is $value")
}
{% endhighlight %}

### Smart Casting

We often write `instanceof` checks before processing a variable e.g. `if (x instanceof String) doSomething()`. In Kotlin, the compiler is smart enough to cast objects when it is certain about type changes. The most common use case of this feature is when you have to use a value after checking it's type:

{% highlight kotlin %}
if (u is User) {
    print(u.email)
}
{% endhighlight %}

If you were to write same snippet in Java, you'd have to either cast `u` once again in the print statement or either store the casted value for future use. In this instance, Kotlin can keep track of type and let you use it freely without caring about the type.

Oh! and by the way `is` and `as` are also operators in Kotlin.

`readability += 1`

### Extension Functions

Here's a scenario, you need a way to check if user is connected to the Internet. You'd find a snippet and then eventually end up storing that in some sort of `Utils` class. Now this `Utils` class has one more habitant along with GOD-knows-how-many other helper methods that are completely unrelated to each other and you carry this file with you in every project and making calls to it for all sorts of functionality. Sounds familiar?

What if you could simply define that functionality in a class that is related to it? Without needing to inherit from it (in case it is a `final` class)? So you can have that functionality in, say `Context` class and then you can simply call your `isConnected()` method from your activities as if it is part of them?
This is exactly what extension functions are for. 

To declare an extension function, we need to prefix its name with a receiver type, i.e. the type being extended. So our `isConnected()` function will look something like this:

{% highlight kotlin %}
fun Context.isConnected(): Boolean {
    // some logic to check if device is connected to the Internet
}
{% endhighlight %}

and then you can use this from inside any descendant of `Context` class as if it is part of standard library while still keeping it in some sort of `Extensions` or `Utils` file.

### Improved Lambdas

We all love lambdas, right? They provide clean and concise logical units that can be passed around as a plug-in functionality. While Java is catching up, they're still not up to the mark when it comes to multi-method lambdas and storing them as expressions.

{% highlight kotlin %}
val sum = { x: Int, y: Int -> x + y }
{% endhighlight %}

This is a simple lambda expression that takes two integer type values and returns their sum. There's a lot of flexibility to them as well, for example, you can omit the method parentheses if the lambda is the only or last argument of a method.

### Higher-Order Functions

Higher-Order Functions, something we always wanted in Java. If you are unfamiliar with the concept, it is simply a function that can accept other functions as parameters or returns a function. 

This is something that can eat a lot of your boilerplate code if used smartly. For example, let's build upon our existing `isConnected()` function and create a function that accepts a block of code and executes it only when connected to Internet. This is a fairly common use case, so you may want to make that an extension function too.

{% highlight kotlin %}
fun Context.doIfNetworkConnected(operation: () -> Unit) {
    if (isConnected()) {
        operation()
    } else {
        // Notify user about no connection
        displayAlert("Please connect to a network!")
    }
}
{% endhighlight %}

When combined with extension functions and lambdas, Higher-Order functions can make your trivial tasks such as checking for the Internet or closing the database after its usage so simple that you can forget about their existence.

### Destructuring Declarations

I found this a very impressive feature in Python that sadly lacked in Java. What destructuring declarations do is simply chop down an object into a number of variables so you wont have to fetch each of its members individually and assign them to variables.

{% highlight kotlin %}
val (name, email, password) = user
{% endhighlight %}

When combined with **Data Classes**, this also paves way for functions that can return multiple values. Something we always wished to have in Java.

For example, let's say we want return `status` and `message` from an HTTP response, all we have to do is declare a `Data Class` and return those two items as it's members. At the other end, we can simply break them.

{% highlight kotlin %}
data class Response(val status: Int, val message: String)

fun handleHttpResponse(r:HttpResponse): Response {
    return Response(r.status, r.message)
}

// Now, to use this function:
val (status, message) = handleHttpResponse(someHttpResponse)
{% endhighlight %}

### Default and Named Parameters

I think most of you would agree that if we had default parameters in Java, it could've saved us from writing countless overloaded methods. We typically write one methods that covers all the possible parameters and then write other variations for it that doesn't do much else but delegate the call to the original method after providing an implementation for missing (or newly added) parameters.

Kotlin solves this problem. Here's an example of extension function for `AppCompatActivity` that displays a `Snackbar` with short duration when the duration is not provided.

{% highlight kotlin %}
fun AppCompatActivity.displayMessage(
  container: ViewGroup, 
  message: String, 
  length: Int = Snackbar.LENGTH_SHORT
) = Snackbar.make(container, message, length).show()
{% endhighlight %}

Not only we can give default values to parameters but also use them with their name, so we can call them independent of their order of declaration. For example, I can call my `displayMessage()` function in any of the given orders:

{% highlight kotlin %}
displayMessage(container = c, message = "Hello!", length = Snackbar.LENGTH_LONG)
displayMessage(message = "Hello!", container = c, length = Snackbar.LENGTH_LONG)
displayMessage(message = "Hello!", length = Snackbar.LENGTH_LONG, container = c)
{% endhighlight %}

### 100% Java Interoperability

Last but most importantly, [Kotlin is 100% interoperable with Java](https://kotlinlang.org/docs/reference/java-interop.html){:target="_blank"}, which means you can use both languages side by side inside the same project and call code from each other without any special treatment. 

Important to mention that Android Studio 3 ships with a Java-to-Kotlin converter, that converts your Java classes with single click of a button. Save it for the time when you completely fall in love with it and decide to convert your existing codebase to Kotlin. ;)

---

## Conclusion

I tried to keep this post as concise as possible and skipped a lot of handy feature such as [Ranges](https://kotlinlang.org/docs/reference/ranges.html){:target="_blank"}, [Properties](https://kotlinlang.org/docs/reference/properties.html){:target="_blank"}, [Operator Overloading](https://kotlinlang.org/docs/reference/operator-overloading.html){:target="_blank"}, [Type Inference](https://kotlinlang.org/docs/reference/basic-types.html){:target="_blank"}, [String Interpolation](https://stackoverflow.com/questions/23086291/format-in-kotlin-string-templates){:target="_blank"} etc in doing so. However, I believe only those discussed will be enough to make you realize how much productivity and ease of use Kotlin can bring to your everyday development. Happy learning! :) 

---

## References

- [Official Kotlin Reference](https://kotlinlang.org/docs/reference/){:target="_blank"}
- [Android Developers Blog](https://android-developers.googleblog.com/2017/05/android-announces-support-for-kotlin.html){:target="_blank"}

---

Image Source - [Wikimedia](https://commons.wikimedia.org/wiki/File:Kotlin_vs_java.jpg){:target="_blank"}.<br />
For suggestions and queries, just [contact me](http://linkedin.com/in/xuhaibahmad){:target="_blank"}.
