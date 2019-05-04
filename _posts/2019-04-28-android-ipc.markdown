---
title: "Inter-Process Communication in Android - Lessons & Learnings"
layout: post
date: 2019-04-28 11:00
image: /assets/images/posts/android_ipc.jpg
headerImage: true
tag:
    - Kotlin
    - Android
    - IPC
category: Blog
blog: true
draft: false
star: true
author: johndoe
description: Lessons learned from implementing IPC in Android for the first time.
---

Recently, I was tasked with building a solution for OTA (Over-the-Air) updates for one of our products at [SodaLabs](https://sodalabs.co/){:target="\_blank"}. Now for those of you who are not familiar with the concept, here's a definition by [TechTarget](https://searchmobilecomputing.techtarget.com/definition/OTA-update-over-the-air-update){:target="\_blank"}:

> An over-the-air update is the wireless delivery of new software or data to mobile devices. Wireless carriers and original equipment manufacturers (OEMs) typically use over-the-air (OTA) updates to deploy firmware and configure phones for use on their networks. The initialization of a newly purchased phone, for example, requires an over-the-air update. With the rise of smartphones, tablets and internet of things (IoT) devices, carriers and manufacturers have also turned to over-the-air updates for deploying new operating systems (OSs) to these devices.

Basically, the updates you receive for your Android, iOS, Windows, Mac or Linux devices are distributed through the mechanism known as OTA programming.

## Going the Sandbox Way

We had a couple of choices around how to build this OTA client, but in the end we decided to build it as a completely separate project. The reason was that both the main product and OTA client were equally complex solutions and we didn't want to make it more complex by adding the OTA as part of the main project.

The OTA would essentially be just a service that runs as a background process and communicate with the main application (the product whose updates this OTA was supposed to manage) via AIDL i.e. the IPC mechanism used by Android.

This was the first time I was going to be working with IPC in general. So unsurprisingly, there was a lot of frustration, learning and fixing through trial and error involved.

My goal in this post is to write about the basics and share some insights from my experience so that you can avoid the confusions that I had to face.

## Inter-Process Communication

Let's start by understand what Inter-Process communication is. Inter-Process Communication or IPC in short, is a mechanism that allows multiple independent processes to communicate and exchange data.

This communication is often achieved through the use of some shared interfaces defined through Interface Description Language (IDL). What it does is basically establish a set of rules that all the interested processes follow. These shared interfaces serves as a common language that all the parties use to communicate with each other.

## How IPC Works In Android

All the applications in Android runs in its own process in a sandbox environment. Which means that each application is independent of others. This isolation helps improve the performance of overall system and in achieving better security through different levels of permissions and access privileges.

Android has its own version of IDL called Android Interface Definition Language (AIDL), which has very much a Java-esque syntax. You basically start by defining an interface (or multiple based on your use case) and then implement the interface in your application and in the client application that wishes to communicate our app.

## Understanding The Architecture

One important thing to understand is that IPC communication in Android is also client-server based. Which means that one application has to act as the server while others can interact with it using the interface it exposes.

Generally that Server application is the one you are writing and which you wish to be controlled from outside sources. Following are the components and steps involved:

- **An AIDL interface:** You define an interface in your server application containing the API that you want to expose to your clients.
- **Implementation of AIDL interface:** Rebuilding your project will generate a java interface from your AIDL file, which you can then use to write a concrete implementation of it.

- **Expose the interface to the clients:** Now that you have a concrete implementation of your AIDL interface, it is time to expose it to your clients by binding it with a Service. This Service can be invoked by external applications and then used a communication channel.

- **Exchanging data via IPC:** On Android, one process cannot normally access the memory of another process. So to talk, they need to decompose their objects into primitives that the operating system can understand, and marshall the objects across that boundary for you. This means that you can only exchange primitive type data through IPC in the most basic from.

- **Writing the parcelable implementations:** Since in most real-world applications you cannot live off just the primitives. Android also provides a mechanism to exchange your Objects through IPC with the use of Parcelables.

These are the 5 main steps required to implement the IPC capabilities in an application. However, admittedly they are not simple and bound to give you a lot of trouble. Let's look at the implementation now.

## Off To The Code

Let's build a very basic application that let's you perform addition. To demonstrate the use of data exchange with both primitives and objects, instead of returning a simple integer value for result, we will return a made-up `Result` object.

Start by creating a simple project with two modules, one serving as a Client while the other as the Server. You can find the link to the complete code at the end of post.

### The AIDL Interface

Now that you have a basic setup, let's first define our very simple AIDL interface in which we want to expose a single method called `performAddition` which takes 2 integer values and returns a made-up `Result` object.

{% highlight java %}
// IAddition.aidl
package com.zuhaibahmad.aidldemo;

import com.zuhaibahmad.aidldemo.Result;
// Declare any non-default types here with import statements

interface IAddition {
    Result performAddition(int numOne, int numTwo);
}
{% endhighlight %}

Now as I mentioned above, since we are using a custom object, we need to define a simple AIDL implementation for it as well.

{% highlight java %}
// Result.aidl
package com.zuhaibahmad.aidldemo;

parcelable Result;
{% endhighlight %}

Build the project and if no errors occur, then you should be able to find a java implementation of of your AIDL interface.

> Once successful, make sure to copy both of your AIDL files to the client project as well.

### Implementation Of The AIDL Interface

Next step is to provide concrete implementation of the AIDL interfaces. For the `Result` object, all you need is a `parcelable` POJO for it. However! You need to write it the old school java way.

I know you love Kotlin and must be thinking of using `parcelize` or at least take advantage of the simpler Kotlin code for data classes, but for some reason Kotlin based parcelables don't work at the time of this writing.

![Oh well!](https://media.giphy.com/media/26u4cIC1w7AKK9LJ6/giphy.gif#center)

So here's your implementation for `Result` class in *pure* java style:

> Make sure to copy this class into client's source code as well since it is needed by client to interpret the `Result` AIDL interface.

{% highlight java %}
package com.zuhaibahmad.aidldemo;

import android.os.Parcel;
import android.os.Parcelable;

public class Result implements Parcelable {
    private int numOne;
    private int numTwo;
    private int result;

    public Result() {}

    public Result(int numOne, int numTwo, int result) {
        this.numOne = numOne;
        this.numTwo = numTwo;
        this.result = result;
    }

    public int getNumOne() {
        return numOne;
    }

    public void setNumOne(int numOne) {
        this.numOne = numOne;
    }

    public int getNumTwo() {
        return numTwo;
    }

    public void setNumTwo(int numTwo) {
        this.numTwo = numTwo;
    }

    public int getResult() {
        return result;
    }

    public void setResult(int numResult) {
        this.result = numResult;
    }

    @Override
    public String toString() {
        return numOne + " + " + numTwo + " = " + result;
    }

    @Override
    public int describeContents() {
        return 0;
    }

    @Override
    public void writeToParcel(Parcel parcel, int i) {
        parcel.writeInt(numOne);
        parcel.writeInt(numTwo);
        parcel.writeInt(result);
    }

    public static final Parcelable.Creator<Result> CREATOR = new Parcelable.Creator<Result>(){

        @Override
        public Result createFromParcel(Parcel parcel) {
            Result res=new Result();
            res.numOne = parcel.readInt();
            res.numTwo = parcel.readInt();
            res.result = parcel.readInt();
            return res;
        }

        @Override
        public Result[] newArray(int size) {
            return new Result[size];
        }
    };
}
{% endhighlight %}

![My eyes bleed](https://media.giphy.com/media/84BjZMVEX3aRG/giphy.gif#center)

Anyways, let's now provide the implementation for our `IAddition` AIDL interface in the server application:

{% highlight kotlin %}
package com.zuhaibahmad.aidldemo

import android.app.Service
import android.content.Intent
import android.os.IBinder
import android.util.Log

class AdditionService : Service() {

    override fun onBind(intent: Intent?): IBinder? {
        Log.e("server", "Binding Service")
        return object : IAddition.Stub() {
            override fun performAddition(numOne: Int, numTwo: Int): Result {
                return Result(numOne, numTwo, numOne + numTwo)
            }
        }
    }
}
{% endhighlight %}

While we are at it, let's also register this service in our `manifest` file. With that our server application will be ready:

{% highlight xml %}
<service
    android:name=".AdditionService"
    android:enabled="true"
    android:exported="true" >
    <intent-filter>
        <action android:name="com.zuhaibahmad.aidldemo.AdditionService" />
    </intent-filter>
</service>
{% endhighlight %}

### Hooking The Service To The Client

Last step is to bind the `AdditionService` in the server application to the client app and starting it remotely. I have a very basic activity with the following UI for client app:

![Client App Screenshot](/assets/images/posts/aidl_demo_ui.png#center)

Let's implement the activity now:

{% highlight kotlin %}
package com.zuhaibahmad.aidldemo

import android.content.ComponentName
import android.content.Context
import android.content.Intent
import android.content.ServiceConnection
import android.os.Bundle
import android.os.IBinder
import android.support.v7.app.AppCompatActivity
import android.util.Log
import kotlinx.android.synthetic.main.activity_main.btEquals
import kotlinx.android.synthetic.main.activity_main.etNumOne
import kotlinx.android.synthetic.main.activity_main.etNumTwo
import kotlinx.android.synthetic.main.activity_main.tvResult

class MainActivity : AppCompatActivity(), ServiceConnection {

    private var isBound: Boolean = false
    private var iRemoteService: IAddition? = null

    // Called when the connection with the service is established
    override fun onServiceConnected(className: ComponentName, service: IBinder) {
        // Following the example above for an AIDL interface,
        // this gets an instance of the IRemoteInterface, which we can use to call on the service
        Log.e("client", "Service connected!")
        iRemoteService = IAddition.Stub.asInterface(service)
        isBound = true
    }

    // Called when the connection with the service disconnects unexpectedly
    override fun onServiceDisconnected(className: ComponentName) {
        Log.e("client", "Service disconnected!")
        iRemoteService = null
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        bindService()

        btEquals.setOnClickListener {
            onPerformAddition()
        }
    }

    private fun onPerformAddition() {
        val numOne = etNumOne.text.toString().toInt()
        val numTwo = etNumTwo.text.toString().toInt()

        tvResult.text = if (isBound) {
            val result = iRemoteService?.performAddition(numOne, numTwo)
            result.toString()
        } else {
            "Service not bound!"
        }
    }

    override fun onDestroy() {
        unbindService()
        super.onDestroy()
    }

    private fun bindService() {
        Log.e("client", "Attempting to bind service")
        val serviceIntent = Intent()
        serviceIntent.setClassName(PACKAGE_NAME, SERVICE_NAME)
        serviceIntent.action = ACTION_REMOTE_BIND
        bindService(serviceIntent, this, Context.BIND_AUTO_CREATE)
    }

    private fun unbindService() {
        if (isBound) {
            // Detach our existing connection.
            unbindService(this)
            isBound = false
        }
    }

    companion object {
        @JvmStatic
        val PACKAGE_NAME: String = "com.zuhaibahmad.aidldemo"

        @JvmStatic
        val SERVICE_NAME: String = "com.zuhaibahmad.aidldemo.AdditionService"

        @JvmStatic
        val ACTION_REMOTE_BIND = "$SERVICE_NAME-remote-bind"
    }
}
{% endhighlight %}

If all goes well, you should be able to compute your additions through the server app using AIDL interfaces.

## Things To Remember

In the end, as you may have realized that this mechanism is not very robust and prone to error. So here are few tips from what I learnt working with Android's IPC about the things that could easily go wrong and make you waste a lot of time.

- Be **very** careful about the intent that you use to trigger the service, a minor typo will lead to service never starting.

- On the same note, if you face a situation at the start where you are not getting the results, verify your service bindings first before diving into debugging. It could just be a faulty service binding and will save you a lot of effort.

- Remember that ONLY primitive types are allowed, so always make sure that you are not accidentally accepting or returning a non-primitive object.

- One of the biggest pain was with threading, I never realized that thread would affect IPC as well. Basically, calls made from the local process are executed in the same thread that is making the call. If this is your main UI thread, that thread continues to execute in the AIDL interface. So be very careful if you are using RxJava or any other async mechanism.

- Remember, Kotlin classes does not work for parcelable implementations with AIDL. At least not at the time of this writing, and what's worse is that it does not give you any error.

---

## Helpful Resources

- This awesome post by [DevArea](https://devarea.com/android-services-and-aidl/){:target="\_blank"}.
- Detailed guide about IPC in Android on [Official Android Resources](https://developer.android.com/guide/components/aidl){:target="\_blank"}.
- Also, this [Medium Article](https://android.jlelse.eu/android-aidl-937daf89e685){:target="\_blank"}.

---

You can find complete source code for this post [here](https://github.com/xuhaibahmad/AIDL-Demo){:target="_blank"}

For suggestions and queries, just [contact me](http://linkedin.com/in/xuhaibahmad){:target="\_blank"}.
