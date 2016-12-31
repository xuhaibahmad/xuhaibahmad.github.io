---
title: "Speed Up Your Android Development with these 10 Libraries (Part 2)"
layout: post
date: 2016-12-31 04:42
image: /assets/images/posts/speed-up-android.jpg
headerImage: true
tag:
- Android
- Java
- Development
- Library
- Productivity
blog: true
draft: false
star: true
author: johndoe
description: Speed Up Your Android Development with these 10 Libraries (Part 2)
---

In the [first part of this post](zuhaibahmad.com/10-libraries-to-speed-up-your-android-development-part-1){:target="_blank"}, we learnt about [ButterKnife](zuhaibahmad.com/10-libraries-to-speed-up-your-android-development-part-1/#butterknife){:target="_blank"}, [SDP](zuhaibahmad.com/10-libraries-to-speed-up-your-android-development-part-1/#sdp){:target="_blank"}, [Joda Time](zuhaibahmad.com/10-libraries-to-speed-up-your-android-development-part-1/#joda-time){:target="_blank"}, [ActiveAndroid](zuhaibahmad.com/10-libraries-to-speed-up-your-android-development-part-1/#activeandroid){:target="_blank"} and [EventBus](zuhaibahmad.com/10-libraries-to-speed-up-your-android-development-part-1/#eventbus){:target="_blank"} libraries to help us speed up our Android development. Now, let's continue from where we left off and explore the remaining libraries in our list.

---

* [Volley](#volley)
* [Okhttp](#okhttp)
* [Parceler](#parceler)
* [Android Annotations](#android-annotations)
* [Espresso](#espresso)

---

## Volley

Network operations have always been a frustrating task for developers mainly due to the excessive amount of tedious work involved. Without the help of any networking library, to read from even the most basic REST APIs that spits data in `JSON` format, you will need to setup an `HttpURLConnection`, `InputStream`/`OutputStream`, a *JSON parser* and you will also have to place all these tasks into an `AsyncTask` to separate them from UI thread and run asynchronously. That's a lot of work with a lot of boilerplate code, probably more than anything else considering the work we are getting done for the amount of code that we write.

[Volley](https://developer.android.com/training/volley/index.html){:target="_blank"} is a HTTP networking library by Google that makes networking tasks easier, helps you get rid of boilerplate and most importantly, it's faster. Some of the key benefits of Volley are:

* Automatic scheduling of network requests.
* Multiple concurrent network connections.
* Support for request prioritization.
* Highly flexible and easily customizable.
* Comes with debugging and tracing tools.

However, Google advices not to use Volley for large download or streaming operations, since Volley holds all responses in memory during parsing. Another limitation is that the only response types supported are `String`, `Image`, `JSONObject`, and `JSONArray` also note that there is also no built-in `XML` support, but due to Volley's flexibility you can build one yourself.

### Setup

Setup for Volley is fairly simple, just add this line to your <span class="evidence">app/build.gradle</span> file and you are good to go:


{% highlight gradle %}
compile 'com.android.volley:volley:1.0.0'
{% endhighlight %}

### Usage

Make sure you have `android.permission.INTERNET` permission in your manifest file.
Create a `RequestQueue` using Volley's convenience method `Volley.newRequestQueue`:

{% highlight java %}
RequestQueue queue = Volley.newRequestQueue(context);
{% endhighlight %}

Specify the URL for request you are making:

{% highlight java %}
final String URL ="http://www.mywebservice.com";
{% endhighlight %}

Create a Volley Request. 

> There are plenty of available request types such as `StringRequest`, `JsonObjectRequest`, `JsonArrayRequest` as well as your custom defined request types.

{% highlight java %}
StringRequest request = new StringRequest(Request.Method.GET, url,
            new Response.Listener<String>() {
    @Override 
    public void onResponse(String response) {
        // Display first 50 characters of the response string. 
        Toast.makeText(context, "Response is: "+ 
            response.substring(0,50), Toast.LENGTH_LONG).show();
    } 
}, new Response.ErrorListener() {
    @Override 
    public void onErrorResponse(VolleyError error) {
        // In case of error
        Toast.makeText(context, "Error in response!", 
            Toast.LENGTH_LONG).show();
    } 
}); 
{% endhighlight %}

Finally, add this request to your Queue:

{% highlight java %}
queue.add(request);
{% endhighlight %}

Although this eliminates a lot of excessive work but still involves a fair amount of code and also it isn't very clean. Nonetheless, it gets the job done, it's robust and very useful for small to medium scale projects.

## Okhttp

Volley is a fairly easy to use and a complete package which provides support ranging from basic String response to Image loading. However, there are some problems. Quoting [this](http://stackoverflow.com/questions/16902716/comparison-of-android-networking-libraries-okhttp-retrofit-volley){:target="_blank"} answer on [Stackoverflow](http://stackoverflow.com/){:target="_blank"} by [CommonsWare](http://stackoverflow.com/users/115145/commonsware){:target="_blank"}:

> it is an unsupported, "throw the code over the wall and do an I\|O presentation on it" library.

which is to say that all it does is **"getting the job done"**. Which comes at a price of *not-so-good-looking* code, lack of multipart requests and most of the load being put on the device memory.

[OkHttp](square.github.io/okhttp/){:target="_blank"} is a networking library developed by Square for sending and receiving HTTP-based network requests. It is built on top of the [Okio](https://github.com/square/okio){:target="_blank"} library, which tries to be more efficient about reading and writing data than the standard Java I/O libraries by creating a shared memory pool. It also is the underlying library for [Retrofit](https://square.github.io/retrofit/){:target="_blank"} library that provides type safety for consuming REST-based APIs.

> Make sure you also check out [Retrofit](https://square.github.io/retrofit/){:target="_blank"} which is another awesome library by [Square](http://square.github.io/){:target="_blank"} which competes with Volley. It is a Type-safe HTTP client for Android and Java that turns your HTTP API into a Java interface.

### Setup

Add this line to your <span class="evidence">app/build.gradle</span> file:

{% highlight gradle %}
compile 'com.squareup.okhttp3:okhttp:3.3.0'
{% endhighlight %}

### Usage

Again, first step is to make sure you have `android.permission.INTERNET` permission in your manifest file.

Specify the URL for request you are making:

{% highlight java %}
final String URL ="http://www.mywebservice.com";
{% endhighlight %}

Instantiate an `OkHttpClient` and create a `Request` object:

{% highlight java %}
OkHttpClient client = new OkHttpClient();
Request request = new Request.Builder().url(URL).build();
{% endhighlight %}

To make a synchronous network call, use the `Client` to create a `Call` object and use the `execute` method.

{% highlight java %}
Response response = client.newCall(request).execute();
{% endhighlight %}

To make asynchronous calls, also create a `Call` object but use the `enqueue` method.

{% highlight java %}
client.newCall(request).enqueue(new Callback() {
    @Override
    public void onFailure(Call call, IOException e) {
        // Do your exception handling here
        e.printStackTrace();
    }

    @Override
    public void onResponse(Call call, final Response response) 
        throws IOException {
        if (!response.isSuccessful()) {
            throw new IOException("Unexpected code " + response);
        } else {
            String text = response.body().string();
            // Display first 50 characters of the response string. 
            Toast.makeText(context, "Response is: "+ 
                text.substring(0,50), Toast.LENGTH_LONG).show();
        }
    }
}
{% endhighlight %}

## Parceler

Now, that we are done with networking, let's take a look at another headache - creating `Parcelable` objects. Although creating `Parcelable` s is faster than using `Serializable` s, creating `Parcelable` objects requires creating a lot of boilerplate code in defining data exactly in the sequence that should be serialized and deserialized. 

[Parceler](https://parceler.org/){:target="_blank"} helps you automate this task. Similar to [Butterknife](http://jakewharton.github.io/butterknife/){:target="_blank"} this library generates the necessary wrapper classes for you at compile time automatically, saving you the repetitive steps required for leveraging the performance benefits of Parcelables.

### Setup

Using Gradle, add this block to <span class="evidence">build.gradle</span> at the project-level, it allows code generation libraries like Butterknife and Parceler for annotation processing:

{% highlight gradle %}
dependencies {
    classpath 'com.neenbedankt.gradle.plugins:android-apt:1.8'
}
{% endhighlight %}

And then in your <span class="evidence">app/build.gradle</span> file, add the following:

{% highlight gradle %}
apply plugin: 'com.neenbedankt.android-apt'

...

dependencies {
    compile 'org.parceler:parceler-api:1.1.1'
    apt 'org.parceler:parceler:1.1.1'
}
{% endhighlight %}


### Usage

By using [Parceler](https://parceler.org/){:target="_blank"} you no longer have to implement the `Parcelable` interface, the `writeToParcel()` or `createFromParcel()` or the `public static final CREATOR`. You simply annotate a POJO with `@Parcel` and Parceler does the rest.

{% highlight java %}
@Parcel
public class User {
    String name;
    int age;

    // Empty constructor required by the Parceler library
    public User() {}

    public User(int age, String name) {
        this.age = age;
        this.name = name;
    }

    public String getName() { return name; }

    public int getAge() { return age; }
}
{% endhighlight %}

To generated Parcelable from POJO, use the Parcels utility class:

{% highlight java %}
Parcelable wrapped = Parcels.wrap(new Example("John Doe", 22));
{% endhighlight %}

To create your POJO from generated `Parcelable`

{% highlight java %}
User user = Parcels.unwrap(wrapped);
user.getName(); // John Doe
user.getAge();  // 22
{% endhighlight %}

> There is an Android Studio Plug-in which makes it even easier to generate `Parcelable`s. We will be looking at it in a future post dedicated to Android Studio Plug-ins.

## Android Annotations

Android Annotations is a framework that speeds up Android development by replacing most of the repetitive tasks and patterns with java annotations. It takes care of the plumbing, and lets you concentrate on what's really important. By simplifying your code, it facilitates its maintenance.

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
    apt "org.androidannotations:androidannotations:4.2.0"
    compile "org.androidannotations:androidannotations-api:4.2.0"
}
{% endhighlight %}

### Usage

Following is an example of a simple `MainActivity` when used without Android Annotations library and when used with it:

#### Before

{% highlight java %}
public class MainActivity extends AppCompatActivity { 
 
    @Override 
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        requestWindowFeature(Window.FEATURE_NO_TITLE);
        getWindow().setFlags(FLAG_FULLSCREEN, FLAG_FULLSCREEN);
 
        setContentView(R.layout.activity_main);
 
        TextView msgTextView = (TextView) findViewById(R.id.tv_msg);
        Button hideButton = (Button) findViewById(R.id.btn_hide);
        hideButton.setOnClickListener(new View.OnClickListener() {
            @Override 
            public void onClick(View v) {
                msgTextView.setVisibility(View.INVISIBLE);
            } 
        }); 
    } 
} 
{% endhighlight %}

#### After

{% highlight java %}
@Fullscreen
@EActivity(R.layout.activity_main)
@WindowFeature(Window.FEATURE_NO_TITLE)
public class MainActivity extends AppCompatActivity { 
 
    @ViewById(R.id.tv_msg)
    TextView mMsgTextView;
 
    @Click(R.id.btn_hide)
    protected void onHideButtonClick() { 
        mMsgTextView.setVisibility(View.INVISIBLE);
    } 
} 
{% endhighlight %}

You can see how clean, concise and readable the code has become after using annotations. There's a lot more to this brilliant library, head over to [official documentation](https://github.com/androidannotations/androidannotations/wiki){:target="_blank"} to find out more features. 

## Espresso

So far we have covered different aspects of our development routine and tried to simplify them with the use of 3rd party libraries. This seems to increase overall performance and productivity in our projects but in the end if you cannot test it, it's probably not worth it. [Testing](){:target="_blank"} has been an integral part of Software Engineering and Android development is no different. In your early days of Android development, you might be testing your applications manually by test driving them every time you added or fixed something. Many might still be doing the same if you have not switched to automated tools yet.

[Espresso](){:target="_blank"} is part of [Android Testing Support Library](){:target="_blank"} provided by [Google](){:target="_blank"}. The basic idea is to get rid of manual testing and let the system do the UI testing for you in matter of seconds. You write intuitive and easy to read tests in the form of each individual action you have to perform in order to get the task done and then finally evaluate the results. All the operations in Espresso follow this simple pattern

> **Find A View** ➜ **Perform Some Action(s)** ➜ **Validate Output**

Each of the steps in your test path are more or less exactly written step by step in Espresso and even a person with little or no knowledge about the domain or your project can easily start writing tests for your application. This ease of use makes it one of the best (if not ***the best***) UI testing framework available for Android.

### Setup

In the <span class="evidence">build.gradle</span> file of your Android app module, you must set a dependency reference to the Espresso library:

{% highlight gradle %}
androidTestCompile 
    'com.android.support.test.espresso:espresso-core:2.2.2'
{% endhighlight %}

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

<sub>Most of the example code and definitions were adopted from official sources to avoid any errors.</sub>