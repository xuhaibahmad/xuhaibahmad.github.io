---
title: "Going Clean With MVP | A Guide To Model-View-Presenter On Android"
layout: post
date: 2017-05-21 03:10
image: /assets/images/posts/mvp.png
headerImage: true
tag:
- Android
- Java
- MVP
- Architecture
blog: true
draft: false
star: true
author: johndoe
description: A step by step guide to architecting your android applications with Model-View-Presenter pattern.
---

> Let's face it! All of us wrote tons and tons of code inside `MainActivity.java`, many still do...

Writing code that *"works"* is fine and acceptable as you start working with a new language or framework. After all, the whole point is to get the work done, right? Not exactly!

We all start from the same place where every "hello world" marks an accomplishment. However, as you grow as an experienced developer, you start to realize that object-oriented programming is more than just writing the code that *works*.

Almost every project requires some level of testing and maintenance throughout its lifecycle. This makes it a strict requirement to write modular code that is easy to read, modify, test and maintain for future collaborators.

> Always code as if the guy who ends up maintaining your code will be a violent psychopath who knows where you live. - John Woods

Obviously, our *\"I love `MainActivity`\"* guy won't find this an easy task. Why? Because when you write every single piece of code in the class where it is needed. You end up with a codebase which of course, gets the job done, but makes it almost impossible to test and maintain in the longer run.

---

## Model-View-Presenter

MVP is an architectural pattern that attempts to decouple the business logic from the views by providing simpler activities with all the important work behind separate presentation layers. This results in a highly readable, maintainable and most importantly testable code.

Some might argue that MVP is not an architectural pattern per se as it is only responsible for managing the presentation of the data. However, we leave the debate here and move on to the "*why?*" aspect.

## Need for MVP?

Android activities are cluttered with closely coupled components where everything is structured in a *"throw the code on the wall"* manner. This makes it an intrinsic problem for android code to be hard to test and extend. Anyone who went through the pain of learning android application testing can tell you that in the beginning it almost feels impossible to test android activities due to unmockable *(is this even a word?)* SDK components and lifecycle management complexities.

When you separate the business logic away from the activity in a separate presentation layer, it automatically makes it trivial to mock and test your code the way you would test any other java class.

The other difficulty apart from code testing is the maintenance of existing codebase. If you are reading this post and understand what I have said so far, I am assuming that you are at that point where you are familiar with the [SOLID](https://en.wikipedia.org/wiki/SOLID_%28object-oriented_design%29){:target="blank"} principles of object oriented design by [Robert C. Martin a.k.a Uncle Bob](https://en.wikipedia.org/wiki/Robert_Cecil_Martin
){:target="blank"}. The last part of the five principles refers to the *Dependency Inversion Principle (DIP)* which encourages the programmers to write code in a way that **it depends upon abstractions instead of the concrete implementations**.

The way MVP is implemented nowadays (or at least they way I learned to) is through `Contract` interfaces that describes the responsibilities of `Views` and the `Presenters`, making the maintenance of code as simple as adding a new method to the interface and its implementation in the corresponding class. Note that this decoupling also allows us for interchangeable views for the same business logic.

Now let's put a full-stop to the theoretical stuff and explore the implementation details of Model, View and Presenter by building a very basic ***hello world MVP app***.

---

Before we go ahead, it's important that you understand that there is no ***"ultimate"*** or ***"best"*** way of implementing MVP. Most people use their own implementations with some modifications, which is completely acceptable as long as the end goal is achieved. Which is to structure your code to decouple the implementation logic from the presentation layer. The way I'm doing it is only the way I like it, if you find another variation of it, don't confuse and use the style that suits you.

## The Model

Model is an interface responsible for managing data. Model’s responsibilities include using APIs, caching data, managing databases and so on. The model can also be an interface that communicates with other modules in charge of these responsibilities.

For the sake of simplicity, I'm not using any model classes in this example but I hope you got the idea.

## The View

Do not confuse this with your `XML` views. Views in MVP are simply the components responsible for managing the user interface. This includes all the UI manipulation operations such as setting text to some `TextView`, toggling visibility of `Views`, displaying animations etc. Basically, everything that has to do with the User Interface, goes into this place.

> Keep your views as dumb as possible.

The idea is to keep your `Presenters` unaware of "how" to present data to the user. All they have is a `View` instance that takes this data as input and performs the presentation tasks. So if you were to display an error to the user in case he forgets to fill a field in the form, all your presenter has to do is call the `view.displayError(errorMessage)` method on the `View` instance.

Now realize that the `Presenter` doesn't know what happens next, whether the error is displayed as a `Toast`, or as a `Dialog` or a `Snackbar` or what color is the message etc. The `View` on the other hand does not know anything about the data it is provided and its origins, it is only interested in accepting the supplied data and manipulate the UI accordingly. This makes it very easy to mock `Views` while testing your business logic so you can focus on the stuff that is important.

The way I like to define my `Views` is through a *View interface* which resides inside the *Contract Interface* for that particular activity.

{% highlight java %}
interface View extends BaseView<Presenter> {

    void displayMessage(String text);

    void displayResult(int result);
}
{% endhighlight %}

Now all I have to do is create a new class and make it implement this interface and we have a candidate that can serve as View for our activity. I guess you might have guessed that we can provide multiple implementations of View as well for different use cases.

{% highlight java %}
public class MainActivity extends AppCompatActivity implements ContractMain.View{

    @BindView(R.id.toolbar)
    Toolbar mToolbar;
    @BindView(R.id.container)
    CoordinatorLayout mContainer;
    @BindView(R.id.numberOneEditText)
    EditText mNumberOneEditText;
    @BindView(R.id.numberTwoEditText)
    EditText mNumberTwoEditText;
    @BindView(R.id.resultTextView)
    TextView mResultTextView;

    private ContractMain.Presenter mPresenter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        ButterKnife.bind(this);
        setSupportActionBar(mToolbar);

        mPresenter = new MainPresenter(this, this);
        mPresenter.start();
    }

    @Override
    public void displayMessage(String text) {
        Snackbar.make(mContainer, text, Snackbar.LENGTH_LONG).show();
    }

    @Override
    public void setPresenter(ContractMain.Presenter presenter) {
        mPresenter = presenter;
    }

    @Override
    public ContractMain.Presenter getPresenter() {
        return mPresenter;
    }

    @OnClick(R.id.addButton)
    public void onAddButtonClicked() {
        String numberOne = mNumberOneEditText.getText().toString();
        String numberTwo = mNumberTwoEditText.getText().toString();

        mPresenter.performAddition(numberOne, numberTwo);
    }

    @Override
    public void displayResult(int result) {
        mResultTextView.setText(String.valueOf(result));
    }
}
{% endhighlight %}

## The Presenter

Think of the presenters as the ***Boss*** here. Similar to how you used to manage everything from your activity in the ***`I Love MainActivity`*** era, Presenters now handles all the management tasks and keeps your activities/fragments less busy. So instead of throwing your `DatabaseHandler`, `ApiHandler`, `PeanutButterMaker` and instances of whatnot in the activity class and make it look like a huge pile of crappy code, you just provide it with the `Presenter` instance and place all the other important stuff inside your `Presenter`.

Now just like the Views, presenters are also defined through a child interface inside the contract interface of the activity that owns it.

{% highlight java %}
interface Presenter extends BasePresenter {

    void performAddition(String numberOne, String numberTwo);

    boolean isEmptyInput(String numOne, String numTwo);
}
{% endhighlight %}

Now all I need to do is to make my activity/fragment implement this interface and provide implementations for the defined operations.

{% highlight java %}
class MainPresenter implements ContractMain.Presenter {

    private final ContractMain.View mView;
    private final Context mContext;

    MainPresenter(ContractMain.View view, Context context) {
        mView = view;
        mContext = context;
    }

    @Override
    public void start() {
        // Do your initialization work here
    }

    @Override
    public void performAddition(String numberOne, String numberTwo) {

        if (isEmptyInput(numberOne, numberTwo)) {
            // Display error message if any of the inputs is empty
            mView.displayMessage(mContext.getString(R.string.error_empty_input));
        } else {
            // Compute and pass the result to view for display
            final int firstNumber = Integer.parseInt(numberOne);
            final int secondNumber = Integer.parseInt(numberTwo);

            final int result = firstNumber + secondNumber;
            mView.displayResult(result);
        }
    }

    @Override
    public boolean isEmptyInput(String numOne, String numTwo) {
        return numOne == null || numOne.length() == 0 ||
            numTwo == null || numTwo.length() == 0;
    }
}
{% endhighlight %}

---

## Outcome

> So you might be wondering what did we achieve apart from dividing our activity into 3 pieces?

The first benefit of using this approach is that we achieved a more modular and scalable architecture. If you were to add **subtract** functionality to this activity, all you would do is define a new method `void performSubtraction(String numberOne, String numberTwo)` in the `Presenter` interface and provide its implementation. Your contracts are the documentation for your code itself.

The second and most important change is that our activity is now fully testable. You could simply mock the View and test all the application logic using your `Presenter`. Below are a couple of tests for the activity we implemented earlier. I'm using `Mockito` for mocking dependencies.

{% highlight java %}
@RunWith(MockitoJUnitRunner.class)
public class MainTest {

    private static final String EMPTY_INPUT_ERROR_MESSAGE
        = "Please Provide Both Numbers!";

    @Mock
    private ContractMain.View mView;
    @Mock
    private Context mContext;

    private ContractMain.Presenter mPresenter;

    @Before
    public void setUp() throws Exception {
        mPresenter = new MainPresenter(mView, mContext);
    }

    @Test
    public void performAddition_shouldReturnResult() {
        final String numberOne = String.valueOf(2);
        final String numberTwo = String.valueOf(3);

        mPresenter.performAddition(numberOne, numberTwo);

        verify(mView).displayResult(5);
    }

    @Test
    public void performAddition_shouldDisplayErrorMessage() {
        final String numberOne = String.valueOf(2);
        final String numberTwo = null;
        // Mock error string because our mock context
        // wouldn't be able to return it
        when(mContext.getString(R.string.error_empty_input))
            .thenReturn(EMPTY_INPUT_ERROR_MESSAGE);

        mPresenter.performAddition(numberOne, numberTwo);

        verify(mView).displayMessage(EMPTY_INPUT_ERROR_MESSAGE);
    }
}
{% endhighlight %}

---

## Conclusion

At this point, it may make sense to your or it won't, but sooner or later, you'll realize that you need some level of modularization in your code if it requires proper testing and be able to remain maintainable for long-term. If not MVP, you might go with one of the several other architectural patterns that are available. For me, MVP makes more sense than any other in the context of Android.

---

<sub>Image rights belong to macoscope.</sub>

---

Source Code is available at [GitHub repository](https://github.com/xuhaibahmad/MVP-Tutorial){:target="blank"}.<br />
For suggestions and queries, just [contact me](http://linkedin.com/in/xuhaibahmad){:target="blank"}.
