---
title: "ActiveAndroid Testing with Robolectric"
layout: post
date: 2017-02-19 04:42
image: /assets/images/posts/robolectric.jpg
headerImage: true
tag:
- Android
- Java
- Testing
- Robolectric
- ActiveAndroid
category: Blog
blog: true
draft: false
star: true
author: johndoe
description: Test ActiveAndroid databases with Robolectric offline tests
---

Testing has been painful for android developers from day one. The problem lies probably in the way the foundations of the android system has been laid. Over the years, many significant improvements have been made yet Android remains one of the most *hard-to-test* platforms to date.

Due to the fact that Android is open-sourced and has a ***HUGE*** community of skilled and creative developers. They left no stone unturned in their attempts to make things better. From new libraries and strategies for testing to the varying styles of organizing and architecting the code (e.g. [MVP](https://en.wikipedia.org/wiki/Model–view–presenter){:target="_blank"} and [Clean](https://8thlight.com/blog/uncle-bob/2012/08/13/the-clean-architecture.html){:target="_blank"} architecture). We have seen attempts made to tackle the problem from every possible angle.

[Robolectric](http://www.robolectric.org){:target="_blank"} was one of the revolutionizing tools that paved way for [TDD](https://en.wikipedia.org/wiki/Test-driven_development){:target="_blank"} on Android. It helps you run most of the tests that requires an Android Device or emulator (Services, Database, UI etc) locally on [JVM](https://en.wikipedia.org/wiki/Java_virtual_machine){:target="_blank"} in a matter of seconds. 

Another brilliant (and popular these days) library that made lives easier was the [ActiveAndroid](www.activeandroid.com){:target="_blank"} [ORM](https://en.wikipedia.org/wiki/Object-relational_mapping){:target="_blank"} that removes all the boilerplate code required to even write the simplest databases in Android while using traditional [SQLite](https://www.sqlite.org/
){:target="_blank"}. However, testing remains as troublesome as it is with the old lad that we are upgrading from. 

Luckily, ActiveAndroid comes in with a partial support form temporary in-memory databases which combined with the power of Robolectric, makes testing no different from your everyday unit tests. In this post, we'll learn to integrate both libraries to setup a testing environment for our database. 

---

* [Adding Dependencies](#adding-dependencies)
* [Writing A Simple App](#writing-a-simple-app)
* [Testing with Robolectric](#testing-with-robolectric)

---

## Adding Dependencies

Before we begin, we need to include Robolectric and ActiveAndroid to our project along with some other necessary libraries. Add the following dependencies to your <span class="evidence">app/build.gradle</span> file:

{% highlight gradle %}
// Support libraries
compile 'com.android.support:design:25.1.0'
compile 'com.android.support:appcompat-v7:25.1.0'
// Active Android
compile 'com.michaelpardo:activeandroid:3.1.0-SNAPSHOT'
// JUnit
testCompile 'junit:junit:4.12'
// Robolectric
testCompile 'org.robolectric:robolectric:3.2.2'
{% endhighlight %}

## Writing A Simple App

We'll start by writing a very simple application that would allow us to save and retrieve information to and from our ActiveAndroid database.

#### MainActivity.java

{% highlight java %}
public class MainActivity extends AppCompatActivity 
    implements View.OnClickListener {

    private LinearLayout mContainer;
    private EditText mNameEditText;
    private EditText mEmailEditText;
    private EditText mContactEditText;
    private Button mSaveButton;
    private Button mRetrieveButton;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mContainer = (LinearLayout) findViewById(R.id.LLContainer);
        mNameEditText = (EditText) findViewById(R.id.ETName);
        mEmailEditText = (EditText) findViewById(R.id.ETEmail);
        mContactEditText = (EditText) findViewById(R.id.ETContact);
        mSaveButton = (Button) findViewById(R.id.BTSave);
        mRetrieveButton = (Button) findViewById(R.id.BTRetrieve);

        mSaveButton.setOnClickListener(this);
        mRetrieveButton.setOnClickListener(this);
    }

    @Override
    public void onClick(View v) {
        int id = v.getId();

        String name = mNameEditText.getText().toString();
        String email = mEmailEditText.getText().toString();
        String contact = mContactEditText.getText().toString();

        if (id == R.id.BTSave) {
            // Validate input
            if (isEmpty(name) || isEmpty(email) || isEmpty(contact)){
                showSnackBar("Please Fill All Fields Before Saving");
                return;
            }

            // Save to database
            boolean isSuccessful = DatabaseUtils.getInstance()
                    .saveToDatabase(name, email, contact);

            // Notify user regarding operation success
            String message = isSuccessful ? "Saved Successfully" 
                : "Error While Saving Item";
            showSnackBar(message);

        } else if (id == R.id.BTRetrieve) {
            // Validate input
            if (isEmpty(name) && isEmpty(email) && isEmpty(contact)){
                showSnackBar("Please Fill At Least One Field " +
                    "Before Retrieving From Database");
                return;
            }

            // Retrieve from database
            Item item = DatabaseUtils.getInstance()
                    .retrieveFromDatabase(name, email, contact);

            // Notify user about error in case of failure
            if (item == null) {
                showSnackBar("Error While Retrieving Item" + 
                    " From Database");
                return;
            }

            // Display item data in relevant fields
            mNameEditText.setText(item.getName());
            mEmailEditText.setText(item.getEmail());
            mContactEditText.setText(item.getContact());

        } else {
            showSnackBar("Invalid Action!");
        }
    }

    private void showSnackBar(String message) {
        Snackbar.make(mContainer, message, Snackbar.LENGTH_SHORT)
            .show();
    }
}
{% endhighlight %}

#### activity_main.xml

{% highlight xml %}
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/LLContainer"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        android:gravity="center"
        android:orientation="vertical">

        <android.support.design.widget.TextInputLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content">

            <EditText
                android:id="@+id/ETName"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:ems="10"
                android:hint="Name"
                android:inputType="textPersonName"/>
        </android.support.design.widget.TextInputLayout>

        <android.support.design.widget.TextInputLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content">

            <EditText
                android:id="@+id/ETEmail"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:ems="10"
                android:hint="Email"
                android:inputType="textEmailAddress"/>
        </android.support.design.widget.TextInputLayout>

        <android.support.design.widget.TextInputLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content">

            <EditText
                android:id="@+id/ETContact"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:ems="10"
                android:hint="Contact Number"
                android:inputType="phone"/>
        </android.support.design.widget.TextInputLayout>
    </LinearLayout>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal">

        <Button
            android:id="@+id/BTSave"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_margin="4dp"
            android:layout_weight=".5"
            android:background="@color/colorPrimary"
            android:text="Save Contact"
            android:textColor="@android:color/white"/>

        <Button
            android:id="@+id/BTRetrieve"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_margin="4dp"
            android:layout_weight=".5"
            android:background="@color/colorPrimary"
            android:text="Retrieve Contact"
            android:textColor="@android:color/white"/>
    </LinearLayout>
</LinearLayout>
{% endhighlight %}

#### Item.java

{% highlight java %}
/**
 * Created by Zuhaib Ahmad on 2/7/2017.
 * <p>
 * Java POJO representing database model
 */
public class Item extends Model {

    public static final String KEY_CONTACT = "contact";
    public static final String KEY_EMAIL = "email";
    public static final String KEY_NAME = "name";

    @Column(name = KEY_NAME)
    private String name;
    @Column(name = KEY_EMAIL)
    private String email;
    @Column(name = KEY_CONTACT)
    private String contact;

    public Item() {
        super();
    }

    public Item(String name, String email, String contact) {
        super();
        this.name = name;
        this.email = email;
        this.contact = contact;
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

    public String getContact() {
        return contact;
    }

    public void setContact(String contact) {
        this.contact = contact;
    }
}
{% endhighlight %}

#### DatabaseUtils.java

{% highlight java %}
/**
 * Created by Zuhaib Ahmad on 1/28/2017.
 * <p>
 * Utility class to handle database operations
 */
public class DatabaseUtils {

    private static DatabaseUtils instance;

    private DatabaseUtils() {
    }

    /**
     * Gets singleton instance.
     *
     * @return the instance
     */
    public static DatabaseUtils getInstance() {
        if (instance == null) {
            instance = new DatabaseUtils();
        }

        return instance;
    }

    /**
     * Retrieves {@link Item} object from database
     *
     * @param name    The name of contact
     * @param email   The email of contact
     * @param contact The number of contact
     * @return The retrieved item
     */
    public Item retrieveFromDatabase(String name, String email, 
        String contact) {
        // Use first non-null parameter for conditional check
        String whereClause = "";
        String key = "";

        if (!isEmpty(name)) {
            whereClause = name;
            key = Item.KEY_NAME;
        } else if (!isEmpty(email)) {
            whereClause = email;
            key = Item.KEY_EMAIL;
        } else if (!isEmpty(contact)) {
            whereClause = contact;
            key = Item.KEY_CONTACT;
        }

        // Query the database and return the fetched item
        return new Select()
                .from(Item.class)
                .where(key + " =?", whereClause)
                .executeSingle();
    }

    /**
     * Saves {@link Item} to database
     *
     * @param name    The name of contact
     * @param email   The email of contact
     * @param contact The number of contact
     * @return The operation success status
     */
    public boolean saveToDatabase(String name, String email, 
        String contact) {
        // Create database item object out of provided parameters
        Item item = new Item(name, email, contact);

        // Save to database and get the id of saved item
        Long id = item.save();

        // Mark operation as successful if returned id is non-zero
        return id > 0;
    }
}
{% endhighlight %}

#### Application.java

{% highlight java %}
/**
 * Created by Zuhaib on 1/22/2016.
 * <p>
 * Application class for global management and data access
 */
public class Application extends android.app.Application {

    @Override
    public void onCreate() {
        super.onCreate();
        // Create Active Android configurations
        Configuration.Builder configuration = 
            new Configuration.Builder(this);
        configuration.addModelClasses(Item.class);
        // Initialize ActiveAndroid DB
        ActiveAndroid.initialize(configuration.create());
    }
}
{% endhighlight %}

## Testing with Robolectric

Before writing tests for our database, we will need to configure our temporary database. To achieve this, we will be writing an alternate version of our <span class="evidence">Application.java</span> class which will replace the original in our tests.

#### TestApplication.java

{% highlight java %}
/**
 * Created by Zuhaib Ahmad on 1/20/2017.
 * <p>
 * Application subclass to be used in Tests
 */

public class TestApplication extends Application {

    @Override
    public void onCreate() {
        super.onCreate();
        // Create configurations for a temporary mock database
        Configuration.Builder configuration = 
            new Configuration.Builder(this).setDatabaseName(null);
        configuration.addModelClasses(Item.class);
        // Initialize ActiveAndroid DB
        ActiveAndroid.initialize(configuration.create());
    }

    @Override
    public void onTerminate() {
        // Dispose temporary database on termination
        ActiveAndroid.dispose();
        super.onTerminate();
    }
}
{% endhighlight %}

> Notice the `setDatabaseName(null)` while building configurations. It allows us to create an anonymous temporary database which we dispose on termination of application.

#### DatabaseUtilsTest.java

{% highlight java %}
/**
 * Created by Zuhaib Ahmad on 2/7/2017.
 * <p>
 * Tests fro database utils
 */
@RunWith(RobolectricTestRunner.class)
@Config(
        constants = BuildConfig.class,
        // Run with TestApplication instead of actual
        application = TestApplication.class,
        // Test against Lollipop
        sdk = 21
)
public class DatabaseUtilsTest {

    private DatabaseUtils mInstance;

    @Before
    public void setUp() throws Exception {
        ShadowLog.stream = System.out;

        mInstance = DatabaseUtils.getInstance();
    }

    @Test
    public void retrieveFromDatabase() throws Exception {
        String name = "Test Retrieve Item";
        String email = "Retrieve@abc.com";
        String contact = "12345";
        Item testItem = new Item(name, email, contact);
        testItem.save();

        // Fetch with name
        Item item1 = mInstance
            .retrieveFromDatabase(name, null, null);
        assertNotNull(item1);
        assertEquals(testItem, item1);

        // Fetch with email
        Item item2 = mInstance
            .retrieveFromDatabase(null, email, null);
        assertNotNull(item2);
        assertEquals(testItem, item2);

        // Fetch with contact
        Item item3 = mInstance
            .retrieveFromDatabase(null, null, contact);
        assertNotNull(item3);
        assertEquals(testItem, item3);

        testItem.delete();
    }

    @Test
    public void saveToDatabase() throws Exception {
        String name = "Test Save Item";
        String email = "Save@abc.com";
        String contact = "67890";

        // Create test object from test parameters
        Item testItem = new Item(name, email, contact);

        // Save test parameters
        boolean isSuccessful = mInstance
            .saveToDatabase(name, email, contact);

        // Check if successfully saved
        assertTrue(isSuccessful);

        // Check if saved item is equal to test object
        Item savedItem = mInstance
            .retrieveFromDatabase(name, null, null);
        assertEquals(testItem.getName(), savedItem.getName());
        assertEquals(testItem.getEmail(), savedItem.getEmail());
        assertEquals(testItem.getContact(), savedItem.getContact());
    }
}
{% endhighlight %}

Run the tests and hopefully you'll see green lights. That's it, you can now locally test your databases just as you would unit test any other part of your application. You can find complete source code of the application [here](https://github.com/xuhaibahmad/Active-Android-Testing-With-Robolectric){:target="_blank"}
