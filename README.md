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
