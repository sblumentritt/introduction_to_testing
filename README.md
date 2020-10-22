> Software testing is an investigation conducted to provide stakeholders with
> information about the quality of the software product or service under test.
> Software testing can also provide an objective, independent view of the
> software to allow the business to appreciate and understand the risks of
> software implementation. Test techniques include the process of executing a
> program or application with the intent of finding software bugs (errors or
> other defects), and verifying that the software product is fit for use.
>
> -- [Software testing. Wikipedia (2020, October 22)](https://en.wikipedia.org/wiki/Software_testing)

[[_TOC_]]

# Properties of good tests

Good tests have five properties which should be the goal for any written test:

1. **Correctness**
2. **Readability**
3. **Completeness**
4. **Demonstrability**
5. **Resilience**

## Correctness

Tests must verify the requirements of the system are met and the test should
only test the wanted parts.

**Do not write**:
- Tests that depend upon known bugs
- Tests that do not actually execute real scenarios

## Readability

Tests should be correct by inspection and should be obvious to the future reader
(including yourself!). A test should be like a novel: setup, action, conclusion,
and it should all make sense.

**Do not write**:
- Too much boilerplate and other distraction
- Not enough context in the test
- Gratuitous use of advanced test framework features

## Completeness

Tests should be written for common inputs, corner cases and outlandish cases.

**Do not write**:
- Tests for APIs that are not yours
- Tests only for the easy cases

## Demonstrability

Tests should serve as a demonstration of how the API works. Tests should act as
documentation and be *the* place to look for examples of good code usage.

- Test in realistic situations
- Test only using the API, just like the users
- If it is hard to test, it is probably hard to use.

**Do not write tests with**:
- Reliance on private methods and friend / test-only methods
- Bad usage in unit tests, suggesting a bad API

## Resilience

Tests should have zero (or minimal) dependency on anything outside the test.

**Do not write tests that fail in all sorts of surprising ways**:

- **Flaky tests**: Tests that can be re-run with the same build in the same
  state and flip from passing to failing (or timing out).
- **Brittle tests**: Tests that can fail for changes unrelated to the code under
  test.
- **Ordering**: Tests that fail if they are not run all together or in a
  particular order.
- **Nonhermeticity**: Tests that fail if anyone else in the company runs the
  same test at the same time.

# How to write testable source code

## Pure functions

When a function is pure, it does not use anything that is not given as arguments
to compute (no global state) nor does it modify anything it is given. The pure
function always just give bake a new copy (no mutation).

## Decoupled design

Before starting to code think about how to break down what to do to have a set
of simple building blocks that each does one very simple thing.

## Defensive programming

Ensure that a piece of code **works under unforeseen circumstances**, to improve
comprehension and predictability and to simplify maintenance.

To program defensively, make sure each method as well as class, variable and
property where applicable:

- Has a clear and single purpose
- Has a clear name with a clear intent (that matches the implementation)
- Does not have any unexpected side effects (for example it should not perform
  operations that is not to be expected)
- Is short and concise
- Is testable
- Validates input
- Handles exceptions
- Has a clear contract
- Has sensible return values
- Does not have any leaky abstractions (internal details and limitations should
  be kept internal)
- Uses comments (with moderation) to explain **why** things are done

## Law of Demeter (LoD)

- Each unit should have only limited knowledge about other units.
- Each unit should only talk to its friends; don't talk to strangers.
- Only talk to your immediate friends.

```cpp
// bad
this.get_a().get_b().do_something()
```

## SOLID design principles

- **Single Responsibility Principle (SRP)**: Each software module should only
  have one reason to change.
- **Open/Closed Principle (OCP)**: Classes should be open for extension but
  closed to modifications.
- **Liskov Substitution Principle (LSP)**: Objects of a superclass shall be
  replaceable with objects of its subclasses without breaking the application.
- **Interface Segregation Principle (ISP)**: No client should be forced to
  depend on methods it does not use.
- **Dependency Inversion Principle (DIP)**: High-level modules should not depend
  on low-level modules; both should depend on abstractions. Abstractions should
  not depend on details. Details should depend upon abstractions.

# Anti-pattern to testable source code

- Adding too many dependencies (create big object graphs)
- Hiding dependencies (for example within constructor, private/static methods)
- Making object hard to initialize
- Creating many execution paths from a single point
- Using train wrecks (trying to use/access a method/property by chaining many
  other objects)
- Using too many arguments for a single method
- Repeating source code logic
- Over-designing
- Functions with side effects
  (https://en.wikipedia.org/wiki/Side_effect_(computer_science))

## Non-Deterministic Factors

```cpp
fn time_of_day() -> string {
    time = date_time.now();
    if (time.hour() >= 0 && time.hour() < 6) {
        return "Night";
    }

    if (time.hour() >= 6 && time.hour() < 12) {
        return "Morning";
    }

    if (time.hour() >= 12 && time.hour() < 18) {
        return "Afternoon";
    }

    return "Evening";
}
```

It is not possible to write a proper state-based unit test for this method.
`date_time.now()` is, essentially, a hidden input. The value will probably
change during program execution or between test runs. 

This method suffers from several issues:

- **It is tightly coupled to the concrete data source.** It is not possible to
  reuse this method for processing date and time retrieved from other sources,
  or passed as an argument.
- **It violates the 'Single Responsibility Principle' (SRP).** The method has
  multiple responsibilities; it consumes the information and also processes it.
- **It lies about the information required to get its job done.** Developer must
  read every line of the actual source code to understand what hidden inputs are
  used and where they come from. The signature alone is not enough understand
  the behavior.
- **It is hard to predict and maintain.** The behavior of a method that depends
  on a mutable global state cannot be predicted by merely reading the source
  code.

Fixing the API:

```cpp
fn time_of_day(date_time time) -> string {
    if (time.hour() >= 0 && time.hour() < 6) {
        return "Night";
    }

    if (time.hour() >= 6 && time.hour() < 12) {
        return "Morning";
    }

    if (time.hour() >= 12 && time.hour() < 18) {
        return "Afternoon";
    }

    return "Evening";
}
```

Notice that this simple refactor solved all the API issues (tight coupling, SRP
violation, unclear and hard to understand API) and the method is now
deterministic (its return value fully depends on the input).

# Glossary

## Unit test

Method that instantiates a small portion of an application and verifies its
behavior **independently** from other parts. A unit is not bound to a single
element but should be as small as possible.

## Integration test

Method to test the modules and components when integrated in a larger system to
verify that they work as expected. In this method many modules are active and
used in a single test.

## Black box testing

Testing technique which focuses on the functionality of the system as a
whole **without knowing** much about the **internal structure and design** of
the item that is being tested and compares the input value with the output
value.

## White box testing

Testing technique in which the tester can see the inner workings of the item
being tested.

## Fuzz testing

An automated testing technique that involves providing invalid, unexpected, or
random data as input.

## Mutation testing

Testing technique in which certain statements of the source code are
changed/mutated to check if the test cases are able to find errors in source
code. The goal is to ensure the quality of test cases in terms of robustness
that is should fail the mutated source code.

## Assertion

A boolean-valued function over the state space connected to a point in the
program, that always should evaluate to true at that point in code execution.

## Mock

Simulated objects that mimic the behavior of real objects in controlled ways.

## Stub

An object that holds predefined data, generally always valid or invalid values,
and uses it to answer calls during tests.

## Fake

Working implementation but will usually substitute its dependencies with
something simpler and easier for a test environment.

Example: An in-memory key/value store vs. a NOR flash backed key/value store.

## Coverage

A metric that measures the amount code that is executed. In testing this metric
can help to determine which branches of conditional statements have been taken
and which source code is not tested at all.

# Sources

- https://www.toptal.com/qa/how-to-write-testable-code-and-why-it-matters
- https://hackernoon.com/how-to-refactor-unwieldy-untestable-code-4a73d75cb80a
- https://www.methodsandtools.com/archive/archive.php?id=103
- https://medium.com/feedzaitech/writing-testable-code-b3201d4538eb
- https://codeaddiction.net/articles/53/writing-testable-code
- https://codeaddiction.net/articles/27/5-practices-that-will-make-your-code-better
- https://en.wikipedia.org/wiki/Code_coverage
- https://www.bfilipek.com/2016/01/micro-benchmarking-libraries-for-c.html
- https://interrupt.memfault.com/blog/unit-testing-basics
- https://blog.trailofbits.com/2019/11/11/test-case-reduction/
- https://martinfowler.com/testing/
- https://www.softwaretestinghelp.com/black-box-testing/
- https://www.softwaretestinghelp.com/white-box-testing-techniques-with-example/
- https://www.softwaretestinghelp.com/what-is-integration-testing/
- https://alysivji.github.io/testing-101-introduction-to-testing.html
- https://en.wikipedia.org/wiki/Fuzzing
- https://www.guru99.com/mutation-testing.html
- https://en.wikipedia.org/wiki/Assertion_(software_development)
- https://en.wikipedia.org/wiki/Mock_object
- https://blog.pragmatists.com/test-doubles-fakes-mocks-and-stubs-1a7491dfa3da
- [Modern C++ Testing with Catch2](https://www.youtube.com/watch?v=Ob5_XZrFQH0)
- [Lessons in Sustainability...](https://www.youtube.com/watch?v=zW-i9eVGU_k)
- [All Your Tests are Terrible...](https://www.youtube.com/watch?v=u5senBJUkPc)
- [Principles of Unit Testing With C++](https://www.youtube.com/watch?v=oOcuJdJJ33g)
- [Structure and Interpretation of Test Cases](https://www.youtube.com/watch?v=tWn8RA_DEic)
