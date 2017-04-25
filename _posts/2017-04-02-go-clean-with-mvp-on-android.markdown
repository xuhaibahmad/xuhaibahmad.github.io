---
title: "Going Clean With MVP | A Guide To Model-View-Presenter Pattern On Android"
layout: post
date: 2017-04-02 03:10
image: /assets/images/posts/mvp.png
headerImage: true
tag:
- Android
- Java
- MVP
- Architecture
blog: true
draft: true
star: true
author: johndoe
description: A step by step guide to architecting your android applications with Model-View-Presenter pattern.
---

> Let's face it! All of us wrote tons and tons of code inside `MainActivity.java`, many still do..

Writing code that "*works*" is fine and acceptable as you start working with a new language or framework. After all, the whole point is to get the work done, right? Not exactly!

We all start from the same place where every "hello world" marks an accomplishment. However, as you grow as an experience developer, you realize that object oriented programming is more than just writing the code that works. Almost every project requires some level of testing and maintenance throughout its lifecycle. This makes it a strict requirement to write modular code that is easy to read, modify, test and maintain for future collaborators.

> Always code as if the guy who ends up maintaining your code will be a violent psychopath who knows where you live. - John Woods

Obviously, our "I love `MainActivity`" guy won't find this an easy task. Why? Because when you write every single piece of code in the class where it is needed. You end up with a codebase which of course, gets the job done, but makes it almost impossible to test and maintain in the longer run.

---

## Model-View-Presenter

MVP is an architectural pattern that attempts to decouple the business logic from the views by providing simpler activities with all the important work behind presentation layer. This results in a highly readable, maintainable and most importantly testable code.

Some might argue that MVP is not an architectural pattern per se as it is only responsible for managing the presentation of the data. However, we leave the debate here and move on to the "*why?*" aspect.

## Need for MVP?

Android activities are cluttered with closely coupled components where everything is structured in a *"throw the code on the wall"* manner. This makes it an intrinsic problem for android code to be hard to test and extend. Anyone who went through the pain of learning to test android applications can tell you that in the beginning it almost feels impossible to test android activities due to unmockable *(is that even a word?)* SDK components and lifecycle management complexities.

When you separate the business logic away from the activity in a separate presentation layer, it automatically makes it trivial to mock or test your code the way you would test any other java class.

The other difficulty apart from code testing is the maintenance of existing codebase. If you understand and aware of what I said so far, then hopefully you are familiar with the [SOLID](https://en.wikipedia.org/wiki/SOLID_%28object-oriented_design%29){:target="_blank"} principles of object oriented design by Robert C. Martin a.k.a Uncle Bob. The last part of the five principles refers to the *Dependency Inversion Principle (DIP)* which encourages the programmers to write code in a way that it depends upon abstractions instead of the concrete implementations.

The way MVP is implemented now a days in through a `Contract` interfaces that describes the responsibilities of `View` and the `Presenter`, making the maintenance of code as simple as add a new method to the interface and its definition in the corresponding class. Note that this decoupling also allows us for interchangeable views for the same business logic.

Now let's put a full-stop to the theoretical stuff and explore the implementation details of Model, View and Presenter by building a very basic hello world MVP app.

---

Before we go ahead, it's important that you understand that there are several ways implementing MVP. Most people use their own implementations with some modifications, which is completely acceptable as long as the end goal is achieved. Which is to structure your code to decouple the implementation logic from presentation layer. The way I'm doing it is only the way I like it, if you find another variation of it, don't confuse and use the style that suits you.

## The Model



## The View


{% highlight java %}
public class MainActivity extends AppCompatActivity implements ContractMain.View {

	// @formatter:off
	@BindView(R.id.toolbar)				Toolbar 				mToolbar;
	@BindView(R.id.container)			CoordinatorLayout 		mContainer;
	@BindView(R.id.numberOneEditText) 	EditText 				mNumberOneEditText;
	@BindView(R.id.numberTwoEditText) 	EditText 				mNumberTwoEditText;
	@BindView(R.id.resultTextView)		TextView 				mResultTextView;
	// @formatter:on

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
		final String numberOne = mNumberOneEditText.getText().toString();
		final String numberTwo = mNumberTwoEditText.getText().toString();

		mPresenter.performAddition(numberOne, numberTwo);
	}

	@Override
	public void displayResult(int result) {
		mResultTextView.setText(String.valueOf(result));
	}
}
{% endhighlight %}


## The Presenter


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
		return numOne == null || numOne.length() == 0 || numTwo == null || numTwo.length() == 0;
	}
}
{% endhighlight %}

--

## Outcome



---

## Conclusion

---

<sub>Image rights belong to macoscope.</sub>

---

Source Code is available at [GitHub repository](){:target="_blank"}.<br />
For suggestions and queries, just [contact me](http://linkedin.com/in/xuhaibahmad){:target="_blank"}.
