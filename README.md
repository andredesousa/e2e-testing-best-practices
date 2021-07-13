# E2E Testing Best Practices

This is a guideline of best practices that you can apply to your frontend project.
Tests are code too.
They should meet the same level of quality as the code being tested.
They can be refactored as well to make them more maintainable and/or readable.
We can split these tests into functional tests and visual tests.

## Table of Contents

1. [Have simple project structure](#have-simple-project-structure)
2. [Given-When-Then](#given-when-then)
3. [Make use of lint rules](#make-use-of-lint-rules)
4. [Test the test code more frequently](#test-the-test-code-more-frequently)
5. [Do not duplicate coverage](#do-not-duplicate-coverage)
6. [Do not test everything](#do-not-test-everything)
7. [Do not ignore flaky tests](#do-not-ignore-flaky-tests)
8. [Do not place assertions inside page objects](#do-not-place-assertions-inside-page-objects)
9. [Do not place assertions inside of try/catch blocks](#do-not-place-assertions-inside-of-trycatch-blocks)
10. [Do not add logic to tests](#do-not-add-logic-to-tests)
11. [Do not use XPath locator](#do-not-use-xpath-locator)
12. [Do not use IDs](#do-not-use-ids)
13. [Do not use text locators](#do-not-use-text-locators)
14. [Use page objects](#use-page-objects)
15. [Test case only call page object's methods](#test-case-only-call-page-objects-methods)
16. [One file, one-page object class](#one-file-one-page-object-class)
17. [Make tests independent at the file level](#make-tests-independent-at-the-file-level)
18. [Navigate to the page under test before each test](#navigate-to-the-page-under-test-before-each-test)
19. [Use async & await](#use-async--await)
20. [Mock all API calls](#mock-all-api-calls)
21. [Replace browser.sleep with browser.wait](#replace-browsersleep-with-browserwait)
22. [Name the screenshots properly](#name-the-screenshots-properly)
23. [Avoid full-page screenshots](#avoid-full-page-screenshots)
24. [Scaling visual testing](#scaling-visual-testing)
25. [Follow the Pattern Library](#follow-the-pattern-library)

## Have simple project structure

A good code project structure will help everyone catch up quicker with the project even it is large-scale.
An `e2e/` folder at the top level contains source files for a set of end-to-end tests that correspond to the root-level application, along with test-specific configuration files.
For a multi-project workspace, application-specific end-to-end tests are in the project root, under `projects/project-name/e2e/`.

## Given-When-Then

The Given-When-Then formula is a template intended to guide the writing of acceptance tests for a User Story:

- The `given` part describes the state of the world before you begin the behavior you're specifying in this scenario.
- The `when` section is that behavior that you're specifying.
- Finally, the `then` section describes the changes you expect due to the specified behavior.

## Make use of lint rules

Linters can catch severe issues like errors that are not thrown correctly and losing information.
A set of plugins were built specifically for inspecting the tests code patterns and discover issues.
For example, [eslint-plugin-protractor](https://www.npmjs.com/package/eslint-plugin-protractor) catches common Protractor errors, follow the best practices for writing Protractor tests, but it also helps to maintain good and reliable element locators.
If we are using Cucumber, it's recommend to use [gherkin-lint](https://www.npmjs.com/package/gherkin-lint), a plugin to analyze feature files and runs linting against the default rules, and the optional rules specified in `.gherkin-lintrc` file.

## Test the test code more frequently

The best practice is to build up a continuous testing pipeline, where the test suite automatically runs every hour, or every time a new change is pushed to code repository.
The earlier you found the issues, the lower cost you need to pay for fixing them.

## Do not duplicate coverage

Don't create functional tests for features coverage by other tests.
Let's say you have created a new UI component.
If you can cover the functionality in a unit test, you should do it.
Unit tests are generally easier to maintain, less flakey, and less expensive to run in the CI pipeline.
Functional Tests should be covering the areas only they can cover.
Usually, these are the big-picture user stories that span many components and views.

## Do not test everything

Try to maximize coverage with fewer tests.
This is difficult because we want to know everything that is going on but the more granular our tests are, the easier it is to track down the actual bugs causing the problem.
It is recommended to check the result of an operation and not the internal implementation.

## Do not ignore flaky tests

E2E tests rely on external systems so extra effort is needed to ensure that they do not randomly fail due to non-deterministic factors such as network conditions, current load on external services, etc.
Each flaky test is different. The test may need to wait longer for a view to load, or it may need something else.
Regardless, my advice is to either shore it up or remove it.
Automatically reporting basic telemetry, such as run-length and failure/success, to a dashboard interface can help identify the problem.

## Do not place assertions inside page objects

The single responsibility principle is one of the famous [SOLID](https://en.wikipedia.org/wiki/SOLID) principles of object-oriented programming which is intended to make software designs more understandable, flexible, and maintainable.
If we are placing verification points inside page object classes, we are violating this principle, unintentionally making test script complicated.

## Do not place assertions inside of try/catch blocks

The purpose of the assertion is that when it fails, it throws the assertion error, so the test engine is notified of that.
Running the tests can be part of the build process, and we get some feedback as to how many of the tests have passed.
Expect errors instead of catching them when an error is expected.

## Do not add logic to tests

Avoid using `if` statements and `for` loops.
When we add logic, our test may pass without testing anything, or may run very slowly.
The logic of code inherently requires a premise, a condition, and a conclusion.
If a test were to include logic, therefore, it would not be a "one-concern" test.

## Do not use XPath locator

It's the slowest and most brittle locator strategy of all, markup is very easily subject to change and therefore XPath locators require a lot of maintenance and XPath expressions are unreadable and very hard to debug.
You must use other locator strategies when possible.

## Do not use IDs

In general, the use of IDs is not recommended. It seems like IDs almost never change, but they can.
There may also be other reasons to avoid adding them to elements, such as if the UI framework you are using inserts and relies on them internally.
A common mistake when using IDs is when we are adding IDs just for testing.
We should prefer the CSS selectors and tag name. Use IDs when no other options are available.

## Do not use text locators

Try to avoid text-based locators such as `by.linkText`, `by.buttonText`, `by.cssContainingText`.
Text for buttons, links, and labels tends to change over time. The tests should not break when we make minor text changes.
Prefer `by.className`, `by.tagName`, `by.css` or `by.id`.

## Use page objects

[Page Object](https://webdriver.io/docs/pageobjects/) is definitely a must-have design pattern.
Page Objects help us write cleaner tests by encapsulating information about the elements on our application page.
A page object can be reused across multiple tests, and if the template of our application changes, we only need to update the page object.
Among many design patterns to develop test cases, Page Object stands out because of the great benefits it brings to the automation teams.

## Test case only call page object's methods

This is a rule of thumb for boosting code reusability and maintainability.
Even if we just need to click on a control, we still must wrap the interaction in a page object's method and call it from the test side.
Doing this, when either the requirement or the AUT's design is changed, makes the only place needing to be refactored just the page object's method.

## One file, one-page object class

By only defining a single page object class in a file, along with user-friendly names for the class and the file, the project will be much more manageable and recognizable no matter of the project's size (small or large-scale).
It is surprisingly easy to find a class or file when designing our page objects this way, so developers should never struggle to look up for any page object class.

## Make tests independent at the file level

[Selenium](https://www.selenium.dev/) can run the tests in parallel with enable sharding.
The files are executed across different browsers as they become available.
Make the tests independent at the file level because the order in which they run is not guaranteed and it's easier to run a test in isolation.

## Navigate to the page under test before each test

This practice assures that the page under test is in a clean state.
It is also recommended to have a suite that navigates through the major routes of the app.
Makes sure the major parts of the application are correctly connected, users usually don't navigate by manually entering urls and gives us confidence about permissions.

## Use async & await

Everyone has heard about the dreadful nightmare in JavaScript named [Callback Hell](http://callbackhell.com/) or [Pyramid of Doom](https://en.wikipedia.org/wiki/Pyramid_of_doom_(programming)).
Luckily, JavaScript has been improved and we now have the async & await feature to deal better with the JavaScript's asynchronous behavior.
Tools like [Protractor](https://www.protractortest.org/) or [Puppeteer](https://pptr.dev/) use Promises.
Together with Promise, `async & await` will help flat out our code and make it simpler and more understandable.

## Mock all API calls

This rule is a bit controversial, in the sense that opinions are very divided when it comes to what the best practice is.
Some developers argue that e2e tests should use mocks for everything in order to avoid external network calls and have a second set of integration tests to test the APIs and database.
Other developers argue that e2e tests should operate on the entire system and be as close to the "real deal" as possible.
To solve this, it's recommended to create a smoke test suite to run in the production environment with "real data".

## Replace browser.sleep with browser.wait

If you are using `browser.sleep()` in Protractor, you are doing it wrong.
The problem with is that it's fragile and will sometimes fail if the system runs slower than normal.
These occasional failures are solved by increasing the timeout value.
This introduces additional latency into the test cycle which increases the feedback time, which is bad.
Instead, you can use `browser.wait()` and [Expected Conditions](https://www.protractortest.org/#/api?view=ProtractorExpectedConditions) to wait for specific conditions in the application to be true.

## Name the screenshots properly

Screenshots can cover small or large areas of the screen.
By default, screenshots are given default names that aren't too descriptive, so include a name so we know what we're actually looking at.
This will provide quick and easy identification of issues without opening the screenshots.

## Avoid full-page screenshots

keep our tests modular or it will get annoying.
Also, bigger screenshots mean longer processing time.
Full-page screenshots typically result in unstable tests because the page has changes in every iteration of the sprint.
Tests must be stable, or they will lose their purpose.

## Scaling visual testing

If you are using git to store the baseline images and project becomes increasingly larger, checkout git storage tools like [Git Annex](https://git-annex.branchable.com/) or [Git Media](https://github.com/alebedev/git-media).
These tools prevent the repo from growing out of control.
However, it is recommended to create a new repository to store the baseline images.
Some people use an independent repository just for visual testing.

## Follow the Pattern Library

If your team has an established component library, writing tests that map to your individual components can make your life much, much easier.
It is much easier to pinpoint bugs by testing one component at a time and writing a test case for each of its states.

## Bibliography

- [15 Best Practices for Building Efficient Protractor Frameworks](https://www.logigear.com/blog/test-automation/15-best-practices-for-building-an-awesome-protractor-framework/)
- [Format, Syntax & Gherkin Test in Cucumber](https://www.guru99.com/gherkin-test-cucumber.html)
- [Lições sobre testes end-to-end automatizados](https://www.casadocodigo.com.br/products/livro-protractor)
- [Protractor style guide](https://github.com/angular/protractor/blob/master/docs/style-guide.md)
- [Testing 101: Introduction to Testing](https://alysivji.github.io/testing-101-introduction-to-testing.html)
- [Visual Testing Handbook](https://storybook.js.org/tutorials/visual-testing-handbook/)
