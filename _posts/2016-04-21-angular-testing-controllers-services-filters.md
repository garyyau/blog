---
layout: post
title: "Angular Testing - Controllers"
date: 2016-04-21
excerpt: "Studying how to create unit tests in Angular, specifically, for Controllers."
tags: [angular, karma, testing, unit, javascript]
comments: true
---

**Karma is .NOT. a replacement for Jasmine.**

When I first saw Karma under the *Testing Angular* module, I thought Karma was a different unit test framework designed to unit test Angular applications. **I was wrong.** After finally going back to watching that module, I learned that Karma is a test runner: a platform designed to make running tests easier.

## Testing Angular Components

How do you test Angular? Unit testing for Angular is *different* based on the different types of Angular components you're trying to test: Controllers, Services, Filters and Directives. I'll have to admit, I haven't watched the videos for how to unit test directives yet since it seems like it's the most annoying one. I have also yet to actually place what I've learned today to practical use, but at least lets document some of it.

*Note: I'm not sure how much of this changes based on using ES2015 classes to define these Angular components.*

### Controllers

For Angular controllers, I normally have a class that defines the controller, and `$inject` any dependencies it needs afterwards. These dependencies are also required as parameters to the class' `constructor`. Here's a sample piece of a unit test file to mock an Angular controller.

{% highlight js %}
describe("MyAppController", () => {

	let $controllerConstructor;
	let scope;
	let mockEventData;

	beforeEach(module("MyApp"));

	beforeEach(inject(($controller, $rootScope) => {
		$controllerConstructor = $controller;
		scope = $rootScope.new();
		mockEventData = sinon.stub({getAllEvents: () => {}});
	}));

	it('should set the scope events to the results of eventData.getAllEvents', () => {
		const mockEvents = {};
		mockEventData.getAllEvents.returns(mockEvents);

		$controllerConstructor(
			"MyAppController",
			{
				'$scope': scope,
				eventData: mockEventData,
			}
		)

		expect(scope.events).toBe(mockEvents);
	});

});
{% endhighlight %}

1. `module("MyApp")` allows you to declare which module (Angular app) this unit test requires to be associated to
2. The `inject` function take a callback function, which asks Angular to look for services based on the name in the parameters and then pass them into the callback.
3. The service `$controller` allows you to construct controllers, needed to access the controllers that are declared on Angular.
4. Because one of the parameters for our controller is `$scope`, we need to initialize an empty scope for the controller to take in. `$rootScope.new()` initializes an empty scope which we can pass into the controller constructor for the constructor to set all the attributes.
5. `mockEventData` is a sample service that we would have created, which has a function called `getAllEvents()`. Once again, we don't want to pass in the real service, so we create a mock one using the sinon library.
6. Using sinon, I have access to functions like `.returns()` to override what a function returns, similar to how it was used in `mockEventData.getAllEvents.returns(mockEvents)`.
7. We declare the controller that we're testing using the `$controllerConstructor`, passing in the controller name and an object defining the parameters that the real controller takes. The constructor will then populate the empty scope we created with attributes, allowing us to access functions and variables defined on the scope.

I think that's about enough for now. I'll have to review this post in the near future to see whether this is the structure I want to use when writing these posts, and also go over the testing methods for Angular Services and Filters.

## Resources

* **Angular Fundamentals** Pluralsight Course by Joe Eames
* (Karma)[https://karma-runner.github.io/0.13/index.html]
