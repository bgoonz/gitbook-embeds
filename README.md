---
coverY: 0
cover: .gitbook/assets/ZAWNEDvpIy.jpg
---

# Initial page

![](https://images.unsplash.com/photo-1633872880116-f19a8e012bfe?crop=entropy\&cs=srgb\&fm=jpg\&ixid=MnwxOTcwMjR8MHwxfHJhbmRvbXx8fHx8fHx8fDE2MzU5MjMxODI\&ixlib=rb-1.2.1\&q=85)

## Test Dirven Development: <a href="#test-dirven-development" id="test-dirven-development"></a>

{% embed url="https://www.spec-india.com/wp-content/uploads/tdd_flow.gif" %}

![](broken-reference)

## Overview

In computing, error detection is a technique that enables a reliable flow during the execution of the program.

One approach to error detection is error checking. This method maintains normal program flow with subsequent explicit checks for anomalies, which are reported using specific return values, an auxiliary global variable, or floating-point status flags.

An exception is the occurrence of an anomaly during the program execution, which interrupts its normal flow. Such an interruption triggers the execution of a pre-registered exception handler.

Exceptions can happen both on the software and the hardware level.

Exceptions in JavaScript

\===============-------------------------------------------------------------------=

A single JavaScript application may have to run on many different operating systems, browsers + plugins, and devices. No matter how many tests you wrote, supporting such an environment of possibilities will ultimately lead to errors.

From an end-user's standpoint, JavaScript is dealing with errors by just failing silently. Things are a bit more complicated under the hood.

A JavaScript code throws an exception when a particular statement generates an error. Instead of executing the next code statement, the [JavaScript engine](https://blog.sessionstack.com/how-javascript-works-inside-the-v8-engine-5-tips-on-how-to-write-optimized-code-ac089e62b12e) checks for the existence of exception handling code.

If no exception handlers have been defined, the engine returns from the function that threw the exception. This process is repeated for each function on the [call stack](https://blog.sessionstack.com/how-does-javascript-actually-work-part-1-b0bacc073cf) until it finds an exception handler. If no exception handler is found and there are no more functions on the stack, the next function on the callback queue is added to the stack by the event loop.

When an exception occurs, an Error Object is created and thrown.

Types of Error Objects

\=================-------------------------------------------------------------------=

There are nine types of built-in error objects in JavaScript, which are the foundation for exception handling:

* Error --- represents generic exceptions. It is most often used for implementing user-defined exceptions.
* EvalError --- occurs when the `eval()` function is used improperly.
* RangeError --- used for errors that occur when a numeric variable or parameter is outside of its valid range.
* ReferenceError --- occurs when a non-existent variable is accessed.
* SyntaxError --- occurs when the JavaScript language rules are broken. For static-typed languages, this happens during compilation time. In JavaScript, it happens during runtime.
* TypeError --- occurs when a value does not match the expected type. Calling a non-existent object method is a common cause of this type of exception.
* URIError --- occurs when `encodeURI()` and `decodeURI()` encounter a malformed URI.
* AggregateError --- when multiple errors need to be reported by an operation, for example by `Promise.any()`.
* InternalError --- occurs when an internal error in the JavaScript engine is thrown. E.g. "too much recursion". This API is not standardized at the moment of writing this article.

You can also define custom error types by inheriting some of the built-in error types.

Throwing Exceptions

\===================-------------------------------------------------------------------

JavaScript allows developers to trigger exceptions via the `throw` statement.

Each of the built-in error objects takes an optional "message" parameter that gives a human-readable description of the error.

It's important to note that you can `throw` any type of object as an Exception, such as Numbers, Strings, Arrays, etc.

These are all valid JavaScript statements.

There are benefits to using the built-in error types instead of other objects since some browsers give special treatment to them, such as the name of the file which caused the exception, the line number, and the stack trace. Some browsers, like Firefox, are populating these properties for all types of objects.

Handling Exceptions

\===================-------------------------------------------------------------------

Now we will see how to make sure that exceptions don't crash our apps.

### The "try" Clause

JavaScript, quite similar to other programming languages, has the `try`, `catch`, `finally` statements, which gives us control over the flow of exceptions on our code.

Here is a sample:

The `try` clause is mandatory and wraps a block of code that potentially can throw an error.

### The "catch" Clause

It is followed by a `catch` block, which wraps JavaScript code that handles the error.

The `catch` clause stops the exception from propagating through the call stack and allows the application flow to continue. The error itself is passed as an argument to the `catch` clause.

Commonly, some code blocks can throw a different kind of exception, and your application can potentially act differently depending on the exception.

JavaScript exposes the `instanceof` operator that can be used to differentiate between the types of exceptions:

It's a valid case to re-`throw` an exception that has been caught. For example, if you catch an exception, the type of which is not relevant to you in this context.

### The "finally" Clause

The `finally` code block is executed after the `try` and `catch` clauses, regardless of any exceptions. The `finally`clause is useful for including clean up code such as closing WebSocket connections or other resources.

Note that the `finally` block will be executed even if a thrown exception is not caught. In such a scenario, the `finally` block is executed, and then the engine continues to go through the functions in the call stack in order until the exception is handled properly or the application is terminated.

Also important to note is that the `finally` block will be executed even if the `try` or `catch` blocks execute a `return` statement.

Let's look at the following example:

By invoking the `foo1()` function, we get `false` as result, even though the `try` block has a `return` statement.

The same applies if we have a `return` statement in a `catch` block:

Invoking `foo2()` also returns `false`.

Handling Exceptions in Asynchronous Code

\===================-------------------------------------------------------------------

Custom errors, extending Error\
When we develop something, we often need our own error classes to reflect specific things that may go wrong in our tasks. For errors in network operations we may need HttpError, for database operations DbError, for searching operations NotFoundError and so on.

Our errors should support basic error properties like message, name and, preferably, stack. But they also may have other properties of their own, e.g. HttpError objects may have a statusCode property with a value like 404 or 403 or 500.

JavaScript allows to use throw with any argument, so technically our custom error classes don’t need to inherit from Error. But if we inherit, then it becomes possible to use obj instanceof Error to identify error objects. So it’s better to inherit from it.

As the application grows, our own errors naturally form a hierarchy. For instance, HttpTimeoutError may inherit from HttpError, and so on.

Extending Error\
As an example, let’s consider a function readUser(json) that should read JSON with user data.

Here’s an example of how a valid json may look:

let json = `{ "name": "John", "age": 30 }`;\
Internally, we’ll use JSON.parse. If it receives malformed json, then it throws SyntaxError. But even if json is syntactically correct, that doesn’t mean that it’s a valid user, right? It may miss the necessary data. For instance, it may not have name and age properties that are essential for our users.

Our function readUser(json) will not only read JSON, but check (“validate”) the data. If there are no required fields, or the format is wrong, then that’s an error. And that’s not a SyntaxError, because the data is syntactically correct, but another kind of error. We’ll call it ValidationError and create a class for it. An error of that kind should also carry the information about the offending field.

Our ValidationError class should inherit from the Error class.

The Error class is built-in, but here’s its approximate code so we can understand what we’re extending:

// The "pseudocode" for the built-in Error class defined by JavaScript itself\
class Error {\
constructor(message) {\
this.message = message;\
[this.name](http://this.name) = "Error"; // (different names for different built-in error classes)\
this.stack = ; // non-standard, but most environments support it\
}\
}\
Now let’s inherit ValidationError from it and try it in action:

class ValidationError extends Error {\
constructor(message) {\
super(message); // (1)\
[this.name](http://this.name) = "ValidationError"; // (2)\
}\
}

function test() {\
throw new ValidationError("Whoops!");\
}

try {\
test();\
} catch(err) {\
alert(err.message); // Whoops!\
alert([err.name](http://err.name)); // ValidationError\
alert(err.stack); // a list of nested calls with line numbers for each\
}\
Please note: in the line (1) we call the parent constructor. JavaScript requires us to call super in the child constructor, so that’s obligatory. The parent constructor sets the message property.

The parent constructor also sets the name property to "Error", so in the line (2) we reset it to the right value.

Let’s try to use it in readUser(json):

class ValidationError extends Error {\
constructor(message) {\
super(message);\
[this.name](http://this.name) = "ValidationError";\
}\
}

// Usage\
function readUser(json) {\
let user = JSON.parse(json);

if (!user.age) {\
throw new ValidationError("No field: age");\
}\
if (\![user.name](http://user.name)) {\
throw new ValidationError("No field: name");\
}

return user;\
}

// Working example with try..catch

try {\
let user = readUser('{ "age": 25 }');\
} catch (err) {\
if (err instanceof ValidationError) {\
alert("Invalid data: " + err.message); // Invalid data: No field: name\
} else if (err instanceof SyntaxError) { // (\*)\
alert("JSON Syntax Error: " + err.message);\
} else {\
throw err; // unknown error, rethrow it (\*\*)\
}\
}\
The try..catch block in the code above handles both our ValidationError and the built-in SyntaxError from JSON.parse.

Please take a look at how we use instanceof to check for the specific error type in the line (\*).

We could also look at [err.name](http://err.name), like this:

// ...\
// instead of (err instanceof SyntaxError)\
} else if ([err.name](http://err.name) == "SyntaxError") { // (\*)\
// ...\
The instanceof version is much better, because in the future we are going to extend ValidationError, make subtypes of it, like PropertyRequiredError. And instanceof check will continue to work for new inheriting classes. So that’s future-proof.

Also it’s important that if catch meets an unknown error, then it rethrows it in the line (\*\*). The catch block only knows how to handle validation and syntax errors, other kinds (caused by a typo in the code or other unknown reasons) should fall through.

Further inheritance\
The ValidationError class is very generic. Many things may go wrong. The property may be absent or it may be in a wrong format (like a string value for age instead of a number). Let’s make a more concrete class PropertyRequiredError, exactly for absent properties. It will carry additional information about the property that’s missing.

class ValidationError extends Error {\
constructor(message) {\
super(message);\
[this.name](http://this.name) = "ValidationError";\
}\
}

class PropertyRequiredError extends ValidationError {\
constructor(property) {\
super("No property: " + property);\
[this.name](http://this.name) = "PropertyRequiredError";\
this.property = property;\
}\
}

// Usage\
function readUser(json) {\
let user = JSON.parse(json);

if (!user.age) {\
throw new PropertyRequiredError("age");\
}\
if (\![user.name](http://user.name)) {\
throw new PropertyRequiredError("name");\
}

return user;\
}

// Working example with try..catch

try {\
let user = readUser('{ "age": 25 }');\
} catch (err) {\
if (err instanceof ValidationError) {\
alert("Invalid data: " + err.message); // Invalid data: No property: name\
alert([err.name](http://err.name)); // PropertyRequiredError\
alert(err.property); // name\
} else if (err instanceof SyntaxError) {\
alert("JSON Syntax Error: " + err.message);\
} else {\
throw err; // unknown error, rethrow it\
}\
}\
The new class PropertyRequiredError is easy to use: we only need to pass the property name: new PropertyRequiredError(property). The human-readable message is generated by the constructor.

Please note that [this.name](http://this.name) in PropertyRequiredError constructor is again assigned manually. That may become a bit tedious – to assign [this.name](http://this.name) = in every custom error class. We can avoid it by making our own “basic error” class that assigns [this.name](http://this.name) = [this.constructor.name](http://this.constructor.name). And then inherit all our custom errors from it.

Let’s call it MyError.

Here’s the code with MyError and other custom error classes, simplified:

class MyError extends Error {\
constructor(message) {\
super(message);\
[this.name](http://this.name) = [this.constructor.name](http://this.constructor.name);\
}\
}

class ValidationError extends MyError { }

class PropertyRequiredError extends ValidationError {\
constructor(property) {\
super("No property: " + property);\
this.property = property;\
}\
}

// name is correct\
alert( new PropertyRequiredError("field").name ); // PropertyRequiredError\
Now custom errors are much shorter, especially ValidationError, as we got rid of the "[this.name](http://this.name) = ..." line in the constructor.

Wrapping exceptions\
The purpose of the function readUser in the code above is “to read the user data”. There may occur different kinds of errors in the process. Right now we have SyntaxError and ValidationError, but in the future readUser function may grow and probably generate other kinds of errors.

The code which calls readUser should handle these errors. Right now it uses multiple ifs in the catch block, that check the class and handle known errors and rethrow the unknown ones.

The scheme is like this:

try {\
...\
readUser() // the potential error source\
...\
} catch (err) {\
if (err instanceof ValidationError) {\
// handle validation errors\
} else if (err instanceof SyntaxError) {\
// handle syntax errors\
} else {\
throw err; // unknown error, rethrow it\
}\
}\
In the code above we can see two types of errors, but there can be more.

If the readUser function generates several kinds of errors, then we should ask ourselves: do we really want to check for all error types one-by-one every time?

Often the answer is “No”: we’d like to be “one level above all that”. We just want to know if there was a “data reading error” – why exactly it happened is often irrelevant (the error message describes it). Or, even better, we’d like to have a way to get the error details, but only if we need to.

The technique that we describe here is called “wrapping exceptions”.

We’ll make a new class ReadError to represent a generic “data reading” error.\
The function readUser will catch data reading errors that occur inside it, such as ValidationError and SyntaxError, and generate a ReadError instead.\
The ReadError object will keep the reference to the original error in its cause property.\
Then the code that calls readUser will only have to check for ReadError, not for every kind of data reading errors. And if it needs more details of an error, it can check its cause property.

Here’s the code that defines ReadError and demonstrates its use in readUser and try..catch:

```
class ReadError extends Error {
  constructor(message, cause) {
    super(message);
    this.cause = cause;
    this.name = 'ReadError';
  }
}

class ValidationError extends Error { /*...*/ }
class PropertyRequiredError extends ValidationError { /* ... */ }

function validateUser(user) {
  if (!user.age) {
    throw new PropertyRequiredError("age");
  }

  if (!user.name) {
    throw new PropertyRequiredError("name");
  }
}

function readUser(json) {
  let user;

  try {
    user = JSON.parse(json);
  } catch (err) {
    if (err instanceof SyntaxError) {
      throw new ReadError("Syntax Error", err);
    } else {
      throw err;
    }
  }

  try {
    validateUser(user);
  } catch (err) {
    if (err instanceof ValidationError) {
      throw new ReadError("Validation Error", err);
    } else {
      throw err;
    }
  }

}

try {
  readUser('{bad json}');
} catch (e) {
  if (e instanceof ReadError) {
    alert(e);
    // Original error: SyntaxError: Unexpected token b in JSON at position 1
    alert("Original error: " + e.cause);
  } else {
    throw e;
  }
}
```

In the code above, readUser works exactly as described – catches syntax and validation errors and throws ReadError errors instead (unknown errors are rethrown as usual).

So the outer code checks instanceof ReadError and that’s it. No need to list all possible error types.

The approach is called “wrapping exceptions”, because we take “low level” exceptions and “wrap” them into ReadError that is more abstract. It is widely used in object-oriented programming.

Summary\
We can inherit from Error and other built-in error classes normally. We just need to take care of the name property and don’t forget to call super.\
We can use instanceof to check for particular errors. It also works with inheritance. But sometimes we have an error object coming from a 3rd-party library and there’s no easy way to get its class. Then name property can be used for such checks.\
Wrapping exceptions is a widespread technique: a function handles low-level exceptions and creates higher-level errors instead of various low-level ones. Low-level exceptions sometimes become properties of that object like err.cause in the examples above, but that’s not strictly required.\
\=================-------------------------------------------------------------------

We won't go into detail about the [internals of async programming in JavaScript](https://blog.sessionstack.com/how-javascript-works-event-loop-and-the-rise-of-async-programming-5-ways-to-better-coding-with-2f077c4438b5) here, but we will see how handling exceptions is done with "callback functions", "promises", and "async/await".

### async/await

Let's define a standard function that just throws an error:

When an error is thrown in an `async` function, a rejected promise will be returned with the thrown error, equivalent to:

Let's see what happens when `foo()` is invoked:

Since `foo()` is `async`, it dispatches a `Promise`. The code does not wait for the `async` function, so there is no actual error to be caught at the moment. The `finally` block is executed and then the `Promise` rejects.

We don't have any code that handles this rejected `Promise`.

This can be handled by just adding the `await` keyword when invoking `foo()` and wrapping the code in an `async` function:

### Promises

Let's define a function that throws an error outside of the `Promise`:

Now let's invoke `foo` with a `string` instead of a `number`:

This will result in an `Uncaught TypeError: x is not a number` since the `catch` of the promise is not being able to handle an error that was thrown outside of the `Promise`.

To catch such errors, you need to use the standard `try` and `catch` clauses:

If `foo` is modified to throw an error inside of the `Promise`:

Now the `catch` statement of the promise will handle the error:

Note that throwing an error inside a `Promise` is the same thing as using the `reject` callback. So it's better to define `foo` like this:

If there is no `catch` method to handle the error inside the `Promise`, the next function from the callback queue will be added to the stack.

### Callback Functions

There are two main rules for working with the error-first callback approach:

1. The first argument of the callback is for the error object. If an error occurred, it will be returned by the first `err` argument. If no error occurred, `err` will be set to `null`.
2. The second argument of the callback is the response data.

If there is an `err` object, it's better not to touch or rely on the `result` parameter.

Dealing with unhandled exceptions

\=====-------------------------------------------------------------------

If your application uses third-party libraries, you have no control over how they deal with exceptions. There are cases when you might want to be able to deal with unhandled exceptions.

### Browser

Browsers expose a `window.onerror` event handler that can be used for this purpose.

Here is how you can use it:

This is what the arguments mean:

* msg --- The message associated with the error, e.g. `Uncaught ReferenceError: foo is not defined`.
* url --- The URL of the script or document associated with the error.
* lineNo --- The line number (if available).
* columnNo --- The column number (if available).
* err --- The Error object associated with this error (if available).

When the function returns true, this prevents the firing of the default event handler.

There can be only one event handler assigned to `window.onerror` because it is a function assignment, and there can only be one function assigned to an event at a time.

This means that if you assign your own `window.onerror`, you will override any previous handler that might have been assigned by third-party libraries. This can be a huge problem, especially for tools such as error trackers, as they will most likely completely stop working.

You can easily work around this problem by using the following trick:

Тhe code above checks if there was a previously defined `window.onerror`, and simply calls it before proceeding. Using this pattern, you can keep adding additional handlers to `window.onerror`.

This approach is highly compatible across browsers (it is supported even in IE6).

An alternative, which doesn't require replacing handlers, is adding an event listener to the `window` object:

This approach is much better, and also widely supported (from IE9 onwards).

### Node.js

The `process` object from the `EventEmmiter` module provides two events for handling errors.

1. `uncaughtException` --- emitted when an uncaught exception bubbles all the way back to the [event loop](https://blog.sessionstack.com/how-javascript-works-event-loop-and-the-rise-of-async-programming-5-ways-to-better-coding-with-2f077c4438b5). By default, Node.js handles such exceptions by printing the stack trace to stderr and exiting with `code 1`. Adding a handler for this event overrides the default behavior. The correct use of the event is to perform synchronous cleanup of allocated resources (e.g. file descriptors, handles, etc) before shutting down the process. It is not safe to resume normal operation afterwards.
2. `unhandledRejection` --- emitted whenever a `Promise` is rejected and no error handler is attached to the promise within a turn of the [event loop](https://blog.sessionstack.com/how-javascript-works-event-loop-and-the-rise-of-async-programming-5-ways-to-better-coding-with-2f077c4438b5). The `unhandledRejection` event is useful for detecting and keeping track of promises that were rejected and which rejections have not yet been handled.

It's really important that error handling is properly taken care of within your code. It's equally important to understand unhandled errors so that you can prioritize and work on them accordingly.

You can do this on your own, which can be quite tricky due to the wide variety of browsers, and all of the different cases that need to be taken care of. Alternatively, you can use some third-party tool to do this for you. No matter what option you choose, it's very important that you have as much information as possible about the error and the user context on how the error was triggered so that you can easily replicate it.

SessionStack is a solution that lets you replay JavaScript errors as if they happened in your browser. You can visually replay the exact user steps that led to the error, see the device, resolution, network, and all of the data that might be needed to connect the dots.

## **Flashcards** <a href="#flashcards" id="flashcards"></a>

### **Asynchronous** <a href="#asynchronous" id="asynchronous"></a>

**1. What are the arguments that the `setTimeout` and `setInterval` methods can accept?**

*
  1. a callback function
  2. a delay time in milliseconds
  3. any number of additional arguments to be passed to the callback

**2. What does the `setTimeout` method return in the Node environment?**

* The `setTimeout` method returns a Timeout object that can be passed to `clearTimeout` to cancel the timeout.

**3. What does the `setTimeout` method return in the browser environment?**

* The `setTimeout` method returns a number representing the unique ID associated with that timeout. The ID number can be passed to `clearTimeout` to cancel the callback from running.

**4. Is JavaScript single-threaded or multi-threaded?**

* Single-threaded.

**5. What is the difference between single-threaded and multi-threaded execution?**

* Single-threaded execution means that only one command can be processed at a time. In multi-threaded execution, more than one command can be processed at a time.

**6. What are the two main data structures that comprise JavaScript's event loop?**

* The call stack and event queue.

**7. When a function is called, what occurs on the call stack?**

* A new stack frame that corresponds to that function call is pushed to the top of the stack.

**8. When a function returns, what occurs on the call stack?**

* The stack frame on top will be popped from the stack.

**9. In the JavaScript event loop, what is the message queue used for?**

* The message queue is used to track events that have occured but cannot be processed yet because the stack is already busy processing other commands. Once the stack is available, the "message" or event handler that is at the front of the queue will be moved to the stack for evaluation.

**10. When an event triggers, what occurs in the JavaScript runtime's message queue?**

* A new message that corresponds to that event is enqueued (added to the back of the queue).

**11. In what order will the following code print to the console?**

```
console.log("first");

setTimeout(function () {
  console.log("second");
}, 0);

console.log("third");
```

* first\
  third\
  second

**12. What must occur for items to be dequeued (removed) from the JavaScript runtime's message queue?**

* The call stack must become empty. This happens when current task is finished executing.

**13. What is the difference between synchronous and asynchronous code?**

* In synchronous code, the commands execute one after the other in order. This means that completion of a "slow" function will block execution of further functions. In asynchronous code, the commands are not guaranteed to occur one after the other; an asynchronous function will not block execution of further functions.

***

## Fundamentals of Testing in Javascript <a href="#fundamentals-of-testing-in-javascript" id="fundamentals-of-testing-in-javascript"></a>

[https://testingjavascript.com/courses/fundamentals-of-testing-in-javascript](https://testingjavascript.com/courses/fundamentals-of-testing-in-javascript)

**Table of Contents**

* 1\. Throw an Error with a Simple Test in JavaScript
* 2\. Abstract Test Assertions into a JavaScript Assertion Library
* 3\. Encapsulate and Isolate Tests by building a JavaScript Testing Framework
* 4\. Support Async Tests with JavaScripts Promises through async await
* 5\. Provide Testing Helper Functions as Globals in JavaScript
* 6\. Verify Custom JavaScript Tests with Jest

### 1. Throw an Error with a Simple Test in JavaScript <a href="#1-throw-an-error-with-a-simple-test-in-javascript" id="1-throw-an-error-with-a-simple-test-in-javascript"></a>

[01-throw-an-error.js](file:///C:/Users/bryan/Downloads/01-throw-an-error.js)

```
$ node 01-throw-an-error.js
```

Throwing an error when a condition fails is the most fundamental aspect of a test.

### 2. Abstract Test Assertions into a JavaScript Assertion Library <a href="#2-abstract-test-assertions-into-a-javascript-assertion-library" id="2-abstract-test-assertions-into-a-javascript-assertion-library"></a>

[02-abstract-assertions.js](file:///C:/Users/bryan/Downloads/02-abstract-assertions.js)

```
$ node 02-abstract-assertions.js
```

An assertion library can be as simple as a function which takes a result and\
returns an object containing a number of different assertions which allows\
the user to determine how they want to evaluate that result against an\
expected value.

### 3. Encapsulate and Isolate Tests by building a JavaScript Testing Framework <a href="#3-encapsulate-and-isolate-tests-by-building-a-javascript-testing-framework" id="3-encapsulate-and-isolate-tests-by-building-a-javascript-testing-framework"></a>

[03-encapsulate-and-isolate-tests.js](file:///C:/Users/bryan/Downloads/03-encapsulate-and-isolate-tests.js)

```
$ node 03-encapsulate-and-isolate-tests.js
```

Our naive implementation works, but prevents subsequent errors from being\
thrown. Additionally, the stack trace indicates the issue is at the location\
where the error was thrown. A better solution would be to allow tests to\
continue running beyond the failed ones, and indicates which test is\
revealing and issue.

This can be fixed by creating a function which takes a title, allowing us to\
name tests, and a callback function which will be responsible for running\
the actual tests, and making use of a try-catch block so as to prevent\
thrown errors from stopping further execution.

### 4. Support Async Tests with JavaScripts Promises through async await <a href="#4-support-async-tests-with-javascripts-promises-through-async-await" id="4-support-async-tests-with-javascripts-promises-through-async-await"></a>

[04-support-async-tests.js](file:///C:/Users/bryan/Downloads/04-support-async-tests.js)

```
$ node 04-support-async-tests.js
```

In order to allow async tests to be run, we need our `test` function to be\
`async`, and we need to prepend the callack invocation with `await`.

### 5. Provide Testing Helper Functions as Globals in JavaScript <a href="#5-provide-testing-helper-functions-as-globals-in-javascript" id="5-provide-testing-helper-functions-as-globals-in-javascript"></a>

[05-provide-testing-helpers-as-globals.js](file:///C:/Users/bryan/Downloads/05-provide-testing-helpers-as-globals.js)

```
$ node --require ./setup-globals.js 05-provide-testing-helpers-as-globals.js
```

Because of the usefulness of test utilities one could extract them into a\
module and import them everywhere.

Another strategy, because we're in a test environment and globals are\
pragmatic in this situation, is to make the helpers available globally.

The `--require ./[file].js` flag allows files to be required before\
executing scripts with the `node` command

### 6. Verify Custom JavaScript Tests with Jest <a href="#6-verify-custom-javascript-tests-with-jest" id="6-verify-custom-javascript-tests-with-jest"></a>

[06-verify-custom-js-tests-with-jest.test.js](file:///C:/Users/bryan/Downloads/06-verify-custom-js-tests-with-jest.test.js)

```
$ npx jest
```

We've implemented the Jest API in our tests, so we can instead run Jest.

What is TDD? In brief: TDD stands for Test-Driven Development. This means that you will develop your code by creating tests first. Those tests will tell you what your code should do. First you will write a test --- it will fail because your code can not do what the test demands --- and after that you will write the code to make it pass. The repetition of "test" to "code" is a cycle. These cycles repeat over and over until your application is complete. After each one of the cycles we will do a refactor to improve the code quality and reorder things a little. Refactoring is a common practice that every experienced software developer should master.

> "Refactoring is the process of changing a software system in such a way that it does not alter the external behavior of the code yet improves its internal structure." --- Martin Fowler

[![](https://res.cloudinary.com/practicaldev/image/fetch/s--FHLYsSyY--/c\_limit%2Cf\_auto%2Cfl\_progressive%2Cq\_auto%2Cw\_880/https://miro.medium.com/max/802/1\*uhUDqedXI41op1WO7QFhtg.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FHLYsSyY--/c\_limit%2Cf\_auto%2Cfl\_progressive%2Cq\_auto%2Cw\_880/https://miro.medium.com/max/802/1\*uhUDqedXI41op1WO7QFhtg.png)

_The cycle, test fail, test pass, refactor_

The tests that we will write for TDD are unit tests.

> UNIT TESTING is a level of software testing where individual units/ components of a software are tested. The purpose is to validate that each unit of the software performs as designed. A unit is the smallest testable part of any software. It usually has one or a few inputs and usually a single output. In procedural programming, a unit may be an individual program, function, procedure, etc. In object-oriented programming, the smallest unit is a method, which may belong to a base/ super class, abstract class or derived/ child class. See More about unit testing [here](http://softwaretestingfundamentals.com/unit-testing/)

Let's learn by practice with an example. Imagine you want to build a calculator with a set of methods: `add, subtract, multiply` and you want to do it with TDD.

First we will need our Calculator class (if you decide to program using classes, if you are using JavaScript you could use modules or any other kind of abstraction).

```
class Calculator {
  // This is an empty class
}
```

For this class we will create our first test:

```
describe("Calculator", function() {
  var calculator;
  beforeEach(function() {
    calculator = new Calculator();
  });
  it("should have an add method", function() {
    expect(calculator.add).toBeDefined()
  });
});
```

This code we just saw is a Test. This test is saying that the calculator should have an `add` method. If we run this test, it will fail, showing something like this:

[![](https://res.cloudinary.com/practicaldev/image/fetch/s--jyEIeSNv--/c\_limit%2Cf\_auto%2Cfl\_progressive%2Cq\_auto%2Cw\_880/https://miro.medium.com/max/1400/0\*ri-Gew1Uv8AAJMXK.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jyEIeSNv--/c\_limit%2Cf\_auto%2Cfl\_progressive%2Cq\_auto%2Cw\_880/https://miro.medium.com/max/1400/0\*ri-Gew1Uv8AAJMXK.png)

If we want to do TDD the next step would be to make the test go green. We will do so by implementing the add method

```
class Calculator {
  add = () => {
    // Empty method
  }
}
```

After we created the method we can see this test run green:

[![](https://res.cloudinary.com/practicaldev/image/fetch/s--utRdbNgI--/c\_limit%2Cf\_auto%2Cfl\_progressive%2Cq\_auto%2Cw\_880/https://miro.medium.com/max/1022/0\*tzzvwgxvkIqAykdW.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--utRdbNgI--/c\_limit%2Cf\_auto%2Cfl\_progressive%2Cq\_auto%2Cw\_880/https://miro.medium.com/max/1022/0\*tzzvwgxvkIqAykdW.png)

This is an example of a cycle of testing and implementation. Ideally we should not test that a method exists, we should test what a method does.

So... now that we understand how the cycles work, let's go a bit further on the implementation:

Test the add method

```
describe('add method', () => {
  it('should return 4 when receiving 2 and 2', () => {
    expect(calculator.add(2, 2)).toEqual(4)
  })
})
```

After we execute the test we see this screen:

[![](https://res.cloudinary.com/practicaldev/image/fetch/s--VvAlMA-S--/c\_limit%2Cf\_auto%2Cfl\_progressive%2Cq\_auto%2Cw\_880/https://miro.medium.com/max/1400/0\*CiP2-UOJZiHaroOq.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VvAlMA-S--/c\_limit%2Cf\_auto%2Cfl\_progressive%2Cq\_auto%2Cw\_880/https://miro.medium.com/max/1400/0\*CiP2-UOJZiHaroOq.png)

Then let's implement the add method.

```
class Calculator {
  add = () => {
    return 4
  }
}
```

After implementing it we launch our tests again and... green!

[![](https://res.cloudinary.com/practicaldev/image/fetch/s--rUCmW0SH--/c\_limit%2Cf\_auto%2Cfl\_progressive%2Cq\_auto%2Cw\_880/https://miro.medium.com/max/990/0\*RDZKmMkJL9opQpUR.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rUCmW0SH--/c\_limit%2Cf\_auto%2Cfl\_progressive%2Cq\_auto%2Cw\_880/https://miro.medium.com/max/990/0\*RDZKmMkJL9opQpUR.png)

JavaScript Unit Testing & TDD

\=========-------------------------------------------------------------------

All about Testing

***

#### JavaScript Unit Testing & TDD <a href="#javascript-unit-testing-tdd" id="javascript-unit-testing-tdd"></a>

#### All about Testing <a href="#all-about-testing" id="all-about-testing"></a>

* _The general idea across all testing frameworks is to allow developers to write code that would specify the behavior of a function or module or class._
* Testing one function at a time is vulnerable to false negatives and false positives.

#### Why do we test? <a href="#why-do-we-test" id="why-do-we-test"></a>

* To make sure everything works.
* Increase flexibility & reduce fear of code.

A**utomated Tests** are known as **Specs**, they:

* Make collaboration easier.
* Produce Documentation.

#### How We Test <a href="#how-we-test" id="how-we-test"></a>

* **Testing Framework** : Runs the tests and presents them to a user.
* **Assertion Libraries** : Backbone of written tests, the code that we use to write our tests.
* **Mocha** : JS testing framework which is highly flexible; supports a variety of assertion libraries.
* **Domain Specific Language** : Refers to a computer language specialized for a particular purpose

#### What do we Test? <a href="#what-do-we-test" id="what-do-we-test"></a>

* The public interface:
* Ask yourself, “What is the public interface of the module or class I’m writing?”
* When in doubt, make sure that you at least have the most important and/or complex parts of that interface working as intended.

![](https://cdn-images-1.medium.com/max/800/0\*-u18Iz0pA\_e0pX2p)![](https://cdn-images-1.medium.com/max/800/0\*Moc1ywM-IYBKtL1l.png)\*The Testing Pyramid\*

#### Unit Tests : The smallest unit of testing. <a href="#unit-tests-the-smallest-unit-of-testing" id="unit-tests-the-smallest-unit-of-testing"></a>

* Used to test smallest pieces in isolation before putting everything together.
* Tests ONE THING.

#### Integration Tests : Test the interactions between two pieces of your application. <a href="#integration-tests-test-the-interactions-between-two-pieces-of-your-application" id="integration-tests-test-the-interactions-between-two-pieces-of-your-application"></a>

#### End to End : Closest automated tests come to testing the actual user experience of your application. <a href="#end-to-end-closest-automated-tests-come-to-testing-the-actual-user-experience-of-your-application" id="end-to-end-closest-automated-tests-come-to-testing-the-actual-user-experience-of-your-application"></a>

**Reading Tests**

*   Most important thing about a test is that it is **readable** and **understandable.**

    describe("avgValue()", function () {\
    it("should return the average of an array of numbers", function () {\
    assert.equal(avgValue(\[10, 20]), 15);\
    });\
    });
* avgValue() => The function we will be testing.
* it(“string”) => describes the desired output.

#### Test-Driven Development <a href="#test-driven-development" id="test-driven-development"></a>

* TDD is a quick repetitive cycle that revolves around first determining what a piece of code should do and writing tests for that behavior _before actually writing any code_.

#### A simple example just to give an idea of what test code looks like… note that it reads more like spoken english than normal code… <a href="#a-simple-example-just-to-give-an-idea-of-what-test-code-looks-like-note-that-it-reads-more-like-spok" id="a-simple-example-just-to-give-an-idea-of-what-test-code-looks-like-note-that-it-reads-more-like-spok"></a>

By [Bryan Guner](https://medium.com/@bryanguner) on [August 25, 2021](https://medium.com/p/72908e7730f5).

[Canonical link](https://medium.com/@bryanguner/javascript-unit-testing-tdd-72908e7730f5)

Exported from [Medium](https://medium.com) on August 31, 2021.

### Wait. What?

Yeah. We passed the test. But the logic of our code... is just wrong! A calculator should not work like that.

If we focus only on passing our tests we can create undesired behaviors. This calculator we coded is passing all of our unit tests but it does not work as a real calculator.

As a software developer you should always try to understand the real needs your software is going to accomplish. Just trying to do what you are told is never the way to become a real professional.

Understanding problems in-depth and giving the correct solution, trying to be pragmatic, and avoiding dogmatism are ways to be a better software professional.

Some developers say that TDD should only focus on solving what the test says. And if the software does not accomplish the real goals is because the test is not enough. I disagree. I say we should understand the real problem. People who write tests (usually developers) tend to forget about some edge cases to test or even miss some required functionalities that were poorly communicated. If we focus only on solving what the test demands we will lack in quality.

I am not talking about programming in a defensive way, trying to solve all the possible ideas we have in our mind, but to deliver the right value to our clients. To understand.

Now, moving back to our test, let's see an example of a more complete test scenario:

```
describe('add method', () => {
  it('should return a SUM when receiving two different numbers', () => {
    for (var i = 0; i < 100; i++) {
      const valueA = Math.round(Math.random() * 100)
      const valueB = Math.round(Math.random() * 100)
      const sum = valueA + valueB
      expect(calculator.add(valueA, valueB)).toEqual(sum)
    }
  })
})
```

What we are doing is generating 100 random value pairs and seeing if the calculator can give the expected result. We could also work with a defined preset of values that we want to test. There are different things we could test: negative numbers `-1`, decimal numbers `0.4`, really big numbers `10000000000000000000...00` or even unexpected values like strings, arrays, `undefined`

In this case we will continue by not trying to be smarter than we need. We will implement our add method and move on.

```
class Calculator {
  add = (a, b) => {
    return a + b
  }
}
```

We could do the same with the different methods for our calculator. First we implement the test and later on we implement the functionality. Just remember not to be too clever and also not too simple. Just doing what the test demands could be enough in some cases, in others may be nothing of value at all.

In our projects we try to do TDD when working on new components, API methods, or isolated pieces of code that can be unit tested.

We don't use TDD as a way to deliver all of our software, --- sometimes other tools and techniques come more in hand for other scenarios --- but we like to practice with it to become better software professionals.

## list of errors: <a href="#list-of-errors" id="list-of-errors"></a>

List of possible JS errors

## \[Types of error] <a href="#types-of-error" id="types-of-error"></a>

Generally speaking, when you do something wrong in code, there are two main types of error that you'll come across:

* **Syntax errors**: These are spelling errors in your code that actually cause the program not to run at all, or stop working part way through --- you will usually be provided with some error messages too. These are usually okay to fix, as long as you are familiar with the right tools and know what the error messages mean!
* **Logic errors**: These are errors where the syntax is actually correct but the code is not what you intended it to be, meaning that program runs successfully but gives incorrect results. These are often harder to fix than syntax errors, as there usually isn't an error message to direct you to the source of the error.

Okay, so it's not quite _that_ simple --- there are some other differentiators as you drill down deeper. But the above classifications will do at this early stage in your career. We'll look at both of these types going forward.

***

![split](https://i.imgur.com/fDXeVIn.png)

## \[An erroneous example] <a href="#an-erroneous-example" id="an-erroneous-example"></a>

***

![devide](https://i.imgur.com/fDXeVIn.png)

***

***

To get started, let's return to our number guessing game --- except this time we'll be exploring a version that has some deliberate errors introduced. Go to Github and make yourself a local copy of \[number-game-errors.html]

1. To get started, open the local copy inside your favorite text editor, and your browser.
2. Try playing the game --- you'll notice that when you press the "Submit guess" button, it doesn't work!

**Note:** You might well have your own version of the game example that doesn't work, which you might want to fix! We'd still like you to work through the article with our version, so that you can learn the techniques we are teaching here. Then you can go back and try to fix your example.

At this point, let's consult the developer console to see if it reports any syntax errors, then try to fix them. You'll learn how below.

***

![split](https://i.imgur.com/fDXeVIn.png)

## \[Fixing syntax errors] <a href="#fixing-syntax-errors" id="fixing-syntax-errors"></a>

Earlier on in the course we got you to type some simple JavaScript commands into the \[developer tools JavaScript console]docs/Learn/Common\_questions/What\_are\_browser\_developer\_tools) (if you can't remember how to open this in your browser, follow the previous link to find out how). What's even more useful is that the console gives you error messages whenever a syntax error exists inside the JavaScript being fed into the browser's JavaScript engine. Now let's go hunting.

1. Go to the tab that you've got `number-game-errors.html` open in, and open your JavaScript console. You should see an error message along the following lines:![](file:///C:/Users/bryan/Downloads/What\_went\_wrong/not-a-function.png)
2. This is a pretty easy error to track down, and the browser gives you several useful bits of information to help you out (the screenshot above is from Firefox, but other browsers provide similar information). From left to right, we've got:
   * A red "x" to indicate that this is an error.
   * An error message to indicate what's gone wrong: "TypeError: guessSubmit.addeventListener is not a function"
   * A "Learn More" link that links through to an MDN page that explains what this error means in greater detail.
   * The name of the JavaScript file, which links through to the Debugger tab of the developer tools. If you follow this link, you'll see the exact line where the error is highlighted.
   * The line number where the error is, and the character number in that line where the error is first seen. In this case, we've got line 86, character number 3.
3.  If we look at line 86 in our code editor, we'll find this line:

    ````
    guessSubmit.addeventListener("click", checkGuess);
    ```js
    ````
4. The error message says "guessSubmit.addeventListener is not a function", which means that the function we're calling is not recognized by the JavaScript interpreter. Often, this error message actually means that we've spelled something wrong. If you are not sure of the correct spelling of a piece of syntax, it is often good to look up the feature on MDN. The best way to do this currently is to search for "mdn _name-of-feature_" with your favorite search engine. Here's a shortcut to save you some time in this instance: \[`addEventListener()`]docs/Web/API/EventTarget/addEventListener).
5. So, looking at this page, the error appears to be that we've spelled the function name wrong! Remember that JavaScript is case sensitive, so any slight difference in spelling or casing will cause an error. Changing `addeventListener` to `addEventListener` should fix this. Do this now.

**Note:** See our \[TypeError: "x" is not a function]Reference/Errors/Not\_a\_function) reference page for more details about this error.

***

![split](https://i.imgur.com/fDXeVIn.png)

## \[Syntax errors round two] <a href="#syntax-errors-round-two" id="syntax-errors-round-two"></a>

1. Save your page and refresh, and you should see the error has gone.
2. Now if you try to enter a guess and press the Submit guess button, you'll see ... another error!![](file:///C:/Users/bryan/Downloads/What\_went\_wrong/variable-is-null.png)
3.  This time the error being reported is "TypeError: lowOrHi is null", on line 78.

    **Note:** \[`Null`]docs/Glossary/Null) is a special value that means "nothing", or "no value". So `lowOrHi` has been declared and initialised, but not with any meaningful value --- it has no type or value.

    **Note:** This error didn't come up as soon as the page was loaded because this error occurred inside a function (inside the `checkGuess() { ... }` block). As you'll learn in more detail in our later \[functions article]docs/Learn/JavaScript/Building\_blocks/Functions), code inside functions runs in a separate scope than code outside functions. In this case, the code was not run and the error was not thrown until the `checkGuess()` function was run by line 86.
4.  Have a look at line 78, and you'll see the following code:

    ```
    lowOrHi.textContent = "Last guess was too high!";
    ```
5.  This line is trying to set the `textContent` property of the `lowOrHi` constant to a text string, but it's not working because `lowOrHi` does not contain what it's supposed to. Let's see why this is --- try searching for other instances of `lowOrHi` in the code. The earliest instance you'll find in the JavaScript is on line 48:

    ```
    const lowOrHi = document.querySelector("lowOrHi");
    ```
6.  At this point we are trying to make the variable contain a reference to an element in the document's HTML. Let's check whether the value is `null` after this line has been run. Add the following code on line 49:

    ```
    console.log(lowOrHi);
    ```

    **Note:** \[`console.log()`]docs/Web/API/console/log) is a really useful debugging function that prints a value to the console. So it will print the value of `lowOrHi` to the console as soon as we have tried to set it in line 48.
7. Save and refresh, and you should now see the `console.log()` result in your console.![](https://developer.mozilla.org/en-US/What\_went\_wrong/console-log-output.png)Sure enough, `lowOrHi`'s value is `null` at this point, so there is definitely a problem with line 48.
8.  Let's think about what the problem could be. Line 48 is using a \[`document.querySelector()`]docs/Web/API/Document/querySelector) method to get a reference to an element by selecting it with a CSS selector. Looking further up our file, we can find the paragraph in question:

    ```
    <p class="lowOrHi"></p>
    ```
9. So we need a class selector here, which begins with a dot (`.`), but the selector being passed into the `querySelector()` method in line 48 has no dot. This could be the problem! Try changing `lowOrHi` to `.lowOrHi` in line 48.
10. Try saving and refreshing again, and your `console.log()` statement should return the `<p>` element we want. Phew! Another error fixed! You can delete your `console.log()` line now, or keep it to reference later on --- your choice.

**Note:** See our \[TypeError: "x" is (not) "y"]Reference/Errors/Unexpected\_type) reference page for more details about this error.

### &#x20;<a href="#undefined" id="undefined"></a>

***

![split](https://i.imgur.com/fDXeVIn.png)

***

## Error

`Error` objects are thrown when runtime errors occur. The `Error` object can also be used as a base object for user-defined exceptions. See below for standard built-in error types.

### [Description](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/Error#description)

Runtime errors result in new `Error` objects being created and thrown.

#### [Error types](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/Error#error\_types) <a href="#error-typeshttpsdevelopermozillaorgen-usdocswebjavascriptreferenceglobal_objectserrorerror_types-per" id="error-typeshttpsdevelopermozillaorgen-usdocswebjavascriptreferenceglobal_objectserrorerror_types-per"></a>

Besides the generic `Error` constructor, there are other core error constructors in JavaScript. For client-side exceptions, see [Exception handling statements](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Control\_flow\_and\_error\_handling#exception\_handling\_statements).

[`EvalError`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/EvalError)

Creates an instance representing an error that occurs regarding the global function [`eval()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/eval).

[`RangeError`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/RangeError)

Creates an instance representing an error that occurs when a numeric variable or parameter is outside of its valid range.

[`ReferenceError`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/ReferenceError)

Creates an instance representing an error that occurs when de-referencing an invalid reference.

[`SyntaxError`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/SyntaxError)

Creates an instance representing a syntax error.

[`TypeError`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/TypeError)

Creates an instance representing an error that occurs when a variable or parameter is not of a valid type.

[`URIError`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/URIError)

Creates an instance representing an error that occurs when [`encodeURI()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/encodeURI) or [`decodeURI()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/decodeURI) are passed invalid parameters.

[`AggregateError`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/AggregateError)

Creates an instance representing several errors wrapped in a single error when multiple errors need to be reported by an operation, for example by [`Promise.any()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/Promise/any).

[`InternalError`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/InternalError)

Creates an instance representing an error that occurs when an internal error in the JavaScript engine is thrown. E.g. "too much recursion".

### [Constructor](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/Error#constructor)

[`Error()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/Error/Error)

Creates a new `Error` object.

### [Static methods](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/Error#static\_methods)

`Error.captureStackTrace()`

A non-standard **V8** function that creates the [`stack`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/Error/Stack) property on an Error instance.

### [Instance properties](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/Error#instance\_properties)

[`Error.prototype.message`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/Error/message)

Error message.

[`Error.prototype.name`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/Error/name)

Error name.

`Error.prototype.description`

A non-standard Microsoft property for the error description. Similar to [`message`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/Error/message).

`Error.prototype.number`

A non-standard Microsoft property for an error number.

[`Error.prototype.fileName`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/Error/fileName)

A non-standard Mozilla property for the path to the file that raised this error.

[`Error.prototype.lineNumber`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/Error/lineNumber)

A non-standard Mozilla property for the line number in the file that raised this error.

[`Error.prototype.columnNumber`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/Error/columnNumber)

A non-standard Mozilla property for the column number in the line that raised this error.

[`Error.prototype.stack`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/Error/Stack)

A non-standard Mozilla property for a stack trace.

![split](https://i.imgur.com/fDXeVIn.png)

## \[Syntax errors round three] <a href="#syntax-errors-round-three" id="syntax-errors-round-three"></a>

1. Now if you try playing the game through again, you should get more success --- the game should play through absolutely fine, until you end the game, either by guessing the right number, or by running out of guesses.
2. At that point, the game fails again, and the same error is spat out that we got at the beginning --- "TypeError: resetButton.addeventListener is not a function"! However, this time it's listed as coming from line 94.
3. Looking at line number 94, it is easy to see that we've made the same mistake here. We again just need to change `addeventListener` to `.addEventListener`. Do this now.

***

![split](https://i.imgur.com/fDXeVIn.png)

## \[A logic error] <a href="#a-logic-error" id="a-logic-error"></a>

***

![devide](https://i.imgur.com/fDXeVIn.png)

***

At this point, the game should play through fine, however after playing through a few times you'll undoubtedly notice that the "random" number you've got to guess is always 1. Definitely not quite how we want the game to play out!

There's definitely a problem in the game logic somewhere --- the game is not returning an error; it just isn't playing right.

1.  Search for the `randomNumber` variable, and the lines where the random number is first set. The instance that stores the random number that we want to guess at the start of the game should be around line number 44:

    ```
    let randomNumber = Math.floor(Math.random()) + 1;
    ```
2.  And the one that generates the random number before each subsequent game is around line 113:

    ```
    randomNumber = Math.floor(Math.random()) + 1;
    ```
3.  To check whether these lines are indeed the problem, let's turn to our friend `console.log()` again --- insert the following line directly below each of the above two lines:

    ```
    console.log(randomNumber);
    ```
4. Save and refresh, then play a few games --- you'll see that `randomNumber` is equal to 1 at each point where it is logged to the console.

### &#x20;<a href="#1" id="1"></a>

***

![split](https://i.imgur.com/fDXeVIn.png)

## \[Working through the logic] <a href="#working-through-the-logic" id="working-through-the-logic"></a>

To fix this, let's consider how this line is working. First, we invoke \[`Math.random()`]/Math/random), which generates a random decimal number between 0 and 1, e.g. 0.5675493843.

```
Math.random();
```

Next, we pass the result of invoking `Math.random()` through \[`Math.floor()`]/Math/floor), which rounds the number passed to it down to the nearest whole number. We then add 1 to that result:

```
Math.floor(Math.random()) + 1;
```

Rounding a random decimal number between 0 and 1 down will always return 0, so adding 1 to it will always return 1. We need to multiply the random number by 100 before we round it down. The following would give us a random number between 0 and 99:

```
Math.floor(Math.random() * 100);
```

Hence us wanting to add 1, to give us a random number between 1 and 100:

```
Math.floor(Math.random() * 100) + 1;
```

Try updating both lines like this, then save and refresh --- the game should now play like we are intending it to!

***

![split](https://i.imgur.com/fDXeVIn.png)

## \[Other common errors] <a href="#other-common-errors" id="other-common-errors"></a>

There are other common errors you'll come across in your code. This section highlights most of them.

### &#x20;<a href="#2" id="2"></a>

***

![split](https://i.imgur.com/fDXeVIn.png)

## \[SyntaxError: missing ; before statement] <a href="#syntaxerror-missing-before-statement" id="syntaxerror-missing-before-statement"></a>

This error generally means that you have missed a semicolon at the end of one of your lines of code, but it can sometimes be more cryptic. For example, if we change this line inside the `checkGuess()` function:

```
let userGuess = Number(guessField.value);
```

to

```
let userGuess === Number(guessField.value);
```

It throws this error because it thinks you are trying to do something different. You should make sure that you don't mix up the assignment operator (`=`) --- which sets a variable to be equal to a value --- with the strict equality operator (`===`), which tests whether one value is equal to another, and returns a `true`/`false` result.

**Note:** See our \[SyntaxError: missing ; before statement]Reference/Errors/Missing\_semicolon\_before\_statement) reference page for more details about this error.

### &#x20;<a href="#3" id="3"></a>

***

![split](https://i.imgur.com/fDXeVIn.png)

## \[The program always says you've won, regardless of the guess you enter] <a href="#the-program-always-says-youve-won-regardless-of-the-guess-you-enter" id="the-program-always-says-youve-won-regardless-of-the-guess-you-enter"></a>

This could be another symptom of mixing up the assignment and strict equality operators. For example, if we were to change this line inside `checkGuess()`:

```
if (userGuess === randomNumber) {
```

to

```
if (userGuess = randomNumber) {
```

the test would always return `true`, causing the program to report that the game has been won. Be careful!

### &#x20;<a href="#4" id="4"></a>

***

![split](https://i.imgur.com/fDXeVIn.png)

## \[SyntaxError: missing] <a href="#syntaxerror-missing" id="syntaxerror-missing"></a>

This one is pretty simple --- it generally means that you've missed the closing parenthesis at the end of a function/method call.

**Note:** See our \[SyntaxError: missing ] reference page for more details about this error.

### &#x20;<a href="#5" id="5"></a>

***

![split](https://i.imgur.com/fDXeVIn.png)

## \[SyntaxError:] <a href="#syntaxerror" id="syntaxerror"></a>

This error usually relates to an incorrectly formed JavaScript object, but in this case we managed to get it by changing

```
function checkGuess() {
```

to

```
function checkGuess( {
```

This has caused the browser to think that we are trying to pass the contents of the function into the function as an argument. Be careful with those parentheses!

### &#x20;<a href="#6" id="6"></a>

***

![split](https://i.imgur.com/fDXeVIn.png)

## \[SyntaxError: missing } after function body]\(What\_went\_wrong#syntaxerror\_missing\_after\_function\_body SyntaxError: missing } after function body") <a href="#syntaxerror-missing-after-function-bodywhat_went_wrongsyntaxerror_missing_after_function_body-syntax" id="syntaxerror-missing-after-function-bodywhat_went_wrongsyntaxerror_missing_after_function_body-syntax"></a>

This is easy --- it generally means that you've missed one of your curly braces from a function or conditional structure. We got this error by deleting one of the closing curly braces near the bottom of the `checkGuess()` function.

### &#x20;<a href="#7" id="7"></a>

***

![split](https://i.imgur.com/fDXeVIn.png)

## \[SyntaxError: <a href="#syntaxerror-1" id="syntaxerror-1"></a>

These errors generally mean that you've left off a string value's opening or closing quote mark. In the first error above, _string_ would be replaced with the unexpected character(s) that the browser found instead of a quote mark at the start of a string. The second error means that the string has not been ended with a quote mark.

For all of these errors, think about how we tackled the examples we looked at in the walkthrough. When an error arises, look at the line number you are given, go to that line and see if you can spot what's wrong. Bear in mind that the error is not necessarily going to be on that line, and also that the error might not be caused by the exact same problem we cited above!

**Note:** See our \[SyntaxError: Unexpected token]Reference/Errors/Unexpected\_token) and \[SyntaxError: unterminated string literal]Reference/Errors/Unterminated\_string\_literal) reference pages for more details about these errors.

Error`objects are thrown when runtime errors occur. The`Error\` object can also be used as a base object for user-defined exceptions. See below for standard built-in error types.

## &#x20;<a href="#8" id="8"></a>

***

![split](https://i.imgur.com/fDXeVIn.png)

## \[Description]/error#description Description") <a href="#descriptionerrordescription-description" id="descriptionerrordescription-description"></a>

Runtime errors result in new `Error` objects being created and thrown.

### &#x20;<a href="#9" id="9"></a>

***

![split](https://i.imgur.com/fDXeVIn.png)

## \[Error types]/error#error\_types Error types") <a href="#error-typeserrorerror_types-error-types" id="error-typeserrorerror_types-error-types"></a>

Besides the generic `Error` constructor, there are other core error constructors in JavaScript. For client-side exceptions, see \[Exception handling statements]Guide/Control\_flow\_and\_error\_handling#exception\_handling\_statements).

***

![split](https://i.imgur.com/fDXeVIn.png)

## \[`EvalError`]/EvalError) <a href="#evalerrorevalerror" id="evalerrorevalerror"></a>

Creates an instance representing an error that occurs regarding the global function \[`eval()`]/eval).

***

![split](https://i.imgur.com/fDXeVIn.png)

## \[`RangeError`]/RangeError) <a href="#rangeerrorrangeerror" id="rangeerrorrangeerror"></a>

Creates an instance representing an error that occurs when a numeric variable or parameter is outside of its valid range.

***

![split](https://i.imgur.com/fDXeVIn.png)

## \[`ReferenceError`]/ReferenceError) <a href="#referenceerrorreferenceerror" id="referenceerrorreferenceerror"></a>

Creates an instance representing an error that occurs when de-referencing an invalid reference.

***

![split](https://i.imgur.com/fDXeVIn.png)

## \[`SyntaxError`]/SyntaxError) <a href="#syntaxerrorsyntaxerror" id="syntaxerrorsyntaxerror"></a>

Creates an instance representing a syntax error.

***

![split](https://i.imgur.com/fDXeVIn.png)

## \[`TypeError`]/TypeError) <a href="#typeerrortypeerror" id="typeerrortypeerror"></a>

Creates an instance representing an error that occurs when a variable or parameter is not of a valid type.

***

![split](https://i.imgur.com/fDXeVIn.png)

## \[`URIError`]/URIError) <a href="#urierrorurierror" id="urierrorurierror"></a>

Creates an instance representing an error that occurs when \[`encodeURI()`]/encodeURI) or \[`decodeURI()`]/decodeURI) are passed invalid parameters.

***

![split](https://i.imgur.com/fDXeVIn.png)

## \[`AggregateError`]/AggregateError) <a href="#aggregateerroraggregateerror" id="aggregateerroraggregateerror"></a>

Creates an instance representing several errors wrapped in a single error when multiple errors need to be reported by an operation, for example by \[`Promise.any()`]/Promise/any).

***

![split](https://i.imgur.com/fDXeVIn.png)

## \[`InternalError`]/InternalError) <a href="#internalerrorinternalerror" id="internalerrorinternalerror"></a>

Creates an instance representing an error that occurs when an internal error in the JavaScript engine is thrown. E.g. "too much recursion".

## &#x20;<a href="#10" id="10"></a>

***

![split](https://i.imgur.com/fDXeVIn.png)

## \[Constructor]/error#constructor Constructor") <a href="#constructorerrorconstructor-constructor" id="constructorerrorconstructor-constructor"></a>

***

![split](https://i.imgur.com/fDXeVIn.png)

## \[`Error()`]/Error/Error) <a href="#errorerrorerror" id="errorerrorerror"></a>

Creates a new `Error` object.

## &#x20;<a href="#11" id="11"></a>

***

![split](https://i.imgur.com/fDXeVIn.png)

## \[Static methods]/error#static\_methods Static methods") <a href="#static-methodserrorstatic_methods-static-methods" id="static-methodserrorstatic_methods-static-methods"></a>

## &#x20;<a href="#12" id="12"></a>

***

![split](https://i.imgur.com/fDXeVIn.png)

## \[Instance properties]/error#instance\_properties Instance properties") <a href="#instance-propertieserrorinstance_properties-instance-properties" id="instance-propertieserrorinstance_properties-instance-properties"></a>

## &#x20;<a href="#13" id="13"></a>

***

![split](https://i.imgur.com/fDXeVIn.png)

## \[Instance methods]/error#instance\_methods Instance methods") <a href="#instance-methodserrorinstance_methods-instance-methods" id="instance-methodserrorinstance_methods-instance-methods"></a>

## &#x20;<a href="#14" id="14"></a>

***

![split](https://i.imgur.com/fDXeVIn.png)

## \[Examples]/error#examples Examples") <a href="#exampleserrorexamples-examples" id="exampleserrorexamples-examples"></a>

### &#x20;<a href="#15" id="15"></a>

***

![split](https://i.imgur.com/fDXeVIn.png)

## \[Throwing a generic error] <a href="#throwing-a-generic-error" id="throwing-a-generic-error"></a>

Usually you create an `Error` object with the intention of raising it using the \[`throw`]Reference/Statements/throw) keyword. You can handle the error using the \[`try...catch`]Reference/Statements/try...catch) construct:

```
try {
  throw new Error('Whoops!')
} catch (e) {
  console.error(e.name + ': ' + e.message)
}
```

### &#x20;<a href="#16" id="16"></a>

***

![split](https://i.imgur.com/fDXeVIn.png)

## \[Handling a specific error type] <a href="#handling-a-specific-error-type" id="handling-a-specific-error-type"></a>

You can choose to handle only specific error types by testing the error type with the error's \[`constructor`]/Object/constructor) property or, if you're writing for modern JavaScript engines, \[`instanceof`]Reference/Operators/instanceof) keyword:

```
try {
  foo.bar()
} catch (e) {
  if (e instanceof EvalError) {
    console.error(e.name + ': ' + e.message)
  } else if (e instanceof RangeError) {
    console.error(e.name + ': ' + e.message)
  }


  else {

    throw e;
  }
}
```

### &#x20;<a href="#17" id="17"></a>

***

![split](https://i.imgur.com/fDXeVIn.png)

## \[Differentiate between similar errors] <a href="#differentiate-between-similar-errors" id="differentiate-between-similar-errors"></a>

Sometimes a block of code can fail for reasons that require different handling, but which throw very similar errors (i.e. with the same type and message).

If you don't have control over the original errors that are thrown, one option is to catch them and throw new `Error` objects that have more specific messages. The original error should be passed to the new `Error` in the constructor `option` parameter (`cause` property), as this ensures that the original error and stack trace are available to higher level try/catch blocks.

The example below shows this for two methods that would otherwise fail with similar errors (`doFailSomeWay()` and `doFailAnotherWay()`):

```
function doWork() {
  try {
    doFailSomeWay();
  } catch (err) {
    throw new Error('Failed in some way', { cause: err });
  }
  try {
    doFailAnotherWay();
  } catch (err) {
    throw new Error('Failed in another way', { cause: err });
  }
}

try {
  doWork();
} catch (err) {
  switch(err.message) {
    case 'Failed in some way':
      handleFailSomeWay(err.cause);
      break;
    case 'Failed in another way':
      handleFailAnotherWay(err.cause);
      break;
  }
}
```

You can also use the `cause` property in \[custom error types]/error#custom\_error\_types), provided the subclasses' constructor passes the `options` parameter when calling `super()`:

```
class MyError extends Error {
  constructor() {

    super(message, options);
  }
}
```

### &#x20;<a href="#18" id="18"></a>

***

![split](https://i.imgur.com/fDXeVIn.png)

## \[Custom Error Types] <a href="#custom-error-types" id="custom-error-types"></a>

You might want to define your own error types deriving from `Error` to be able to `throw new MyError()` and use `instanceof MyError` to check the kind of error in the exception handler. This results in cleaner and more consistent error handling code.

See ["What's a good way to extend Error in JavaScript?"](https://stackoverflow.com/questions/1382107/whats-a-good-way-to-extend-error-in-javascript) on StackOverflow for an in-depth discussion.

![devide](https://i.imgur.com/fDXeVIn.png)

***

![split](https://i.imgur.com/fDXeVIn.png)

## ES6 Custom Error Class <a href="#es6-custom-error-class" id="es6-custom-error-class"></a>

**Warning:** Versions of Babel prior to 7 can handle `CustomError` class methods, but only when they are declared with \[Object.defineProperty()]/Object/defineProperty). Otherwise, old versions of Babel and other transpilers will not correctly handle the following code without [additional configuration](https://github.com/loganfsmyth/babel-plugin-transform-builtin-extend).

**Note:** Some browsers include the `CustomError` constructor in the stack trace when using ES2015 classes.

```
class CustomError extends Error {
  constructor(foo = 'bar', ...params) {

    super(...params)


    if (Error.captureStackTrace) {
      Error.captureStackTrace(this, CustomError)
    }

    this.name = 'CustomError'

    this.foo = foo
    this.date = new Date()
  }
}

try {
  throw new CustomError('baz', 'bazMessage')
} catch(e) {
  console.error(e.name)
  console.error(e.foo)
  console.error(e.message)
  console.error(e.stack)
}
```

#### ⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯ <a href="#19" id="19"></a>

***

![split](https://i.imgur.com/fDXeVIn.png)---

````
#ES5 Custom Error Object

**Warning:** All browsers include the `CustomError` constructor in the stack trace when using a prototypal declaration.

```js
function CustomError(foo, message, fileName, lineNumber) {
  var instance = new Error(message, fileName, lineNumber);
  instance.name = 'CustomError';
  instance.foo = foo;
  Object.setPrototypeOf(instance, Object.getPrototypeOf(this));
  if (Error.captureStackTrace) {
    Error.captureStackTrace(instance, CustomError);
  }
  return instance;
}

CustomError.prototype = Object.create(Error.prototype, {
  constructor: {
    value: Error,
    enumerable: false,
    writable: true,
    configurable: true
  }
});

if (Object.setPrototypeOf){
  Object.setPrototypeOf(CustomError, Error);
} else {
  CustomError.__proto__ = Error;
}

try {
  throw new CustomError('baz', 'bazMessage');
} catch(e){
  console.error(e.name);
  console.error(e.foo);
  console.error(e.message);
}
````

***

***

***

***

## Challenge:

There is a made up API URL (`http://great.jokes/christmas`) that you can call to a get list of Christmas jokes in JSON format.

### Your challenge

* Write an async function which takes an `apiUrl` and `jokeId` which returns a promise.
* The data will need to be filtered to get the specified joke by `id`.
* When you got the joke it should be accessible through a simple API of `saySetup` and `sayPunchLine` methods.

Handle error cases

* If a joke can't be found throw an error message in this format `new Error('No jokes found id: {jokeId}')`.
* Getting jokes from a another API URL may return a different data shape, throw this error message `new Error('No jokes at url: {url}')` for an unexpected shape.

_Throw error in a promise style_

### Info

Get the data using the mocked `fetch(url)` function, which implements the basics of the fetch api. [Learn about fetch](https://davidwalsh.name/fetch). [Learn about async/await](https://javascript.info/async-await).

Joke data shape:

```
{
  jokes: [{
    id: 101,
    setup: "Who is Santa's favorite singer?",
    punchLine: "Elf-is Presley!"
  },
...moreJokes]
// Use for your tests ^^
```

***

***

***

***

239

The only standard field Error object has is the message property. (See MDN, or EcmaScript Language Specification, section 15.11) Everything else is platform specific.

Mosts environments set the stack property, but fileName and lineNumber are practically useless to be used in inheritance.

So, the minimalistic approach is:

function MyError(message) {\
[this.name](http://this.name) = 'MyError';\
this.message = message;\
this.stack = (new Error()).stack;\
}\
MyError.prototype = new Error; // <-- remove this if you do not\
// want MyError to be instanceof Error\
You could sniff the stack, unshift unwanted elements from it and extract information like fileName and lineNumber, but doing so requires information about the platform JavaScript is currently running upon. Most cases that is unnecessary -- and you can do it in post-mortem if you really want.

Safari is a notable exception. There is no stack property, but the throw keyword sets sourceURL and line properties of the object that is being thrown. Those things are guaranteed to be correct.

## Static Analysis Testing JavaScript Applications

**Table of Contents**

* 1\. Lint JavaScript by configuring and running ESLint
* 2\. Format Code by installing and running Prettier
* 3\. Configure Prettier
* 4\. Disable Unnecessary ESLint Stylistic Rules with eslint-config-prettier
* 5\. Validate all files are formatted when linting
* 6\. Avoid Common Errors with Flow Type Definitions
* 7\. Validate Code in a pre-commit git Hook with husky
* 8\. Auto-format all files and validate relevant files in a precommit script with `lint-staged`

### 1. Lint JavaScript by configuring and running ESLint

```
$ npm run lint
```

Eslint rules with `error` will exit the process and fail a build.

Eslint rules with `warn` will continue to run the process and not fail a\
build.

Eslint rules with `off` will ignore the rule entirely.

One can extend one's eslint config using the `extends` property.\
`eslint:recommended` is a built-in config.

The `env` property can be used to set the environment against which files\
should be evaluated.

### 2. Format Code by installing and running Prettier

`prettier` can be used to format code in files via CLI:

```
$ prettier --write path/to/files
```

`prettier` can be used to format markdown and graphql, too.

### 3. Configure Prettier

`prettier` allows for a `.prettierignore` to ignore formatting of generated\
files

### 4. Disable Unnecessary ESLint Stylistic Rules with eslint-config-prettier

`extend`ed eslint configs take precedence from the end of the array.

`rules` in the the eslint config take precedence over any extensions.

### 5. Validate all files are formatted when linting

`prettier` can be run with a `--list-different` flag which exits with a\
non-zero code if there are any files that are not formatted correctly. This\
can be used with `ghooks` to ensure all team members are using `prettier`.

npm scripts can forward command line arguments to each other using the `--`\
operator:

```
  ...
  "scripts": {
    "myscript": "some-command",
    "myscript:alpha": "npm run myscript -- --some-flag"
  }
  ...
```

```
$ npm run myscript -- --some-flag

#  or
<p class="mume-header " id="or"></p>

$ npm run myscript:alpha
```

### 6. Avoid Common Errors with Flow Type Definitions

Use `@flow` at the top of files that you want to use `flow` to make type\
strict after installing `flow-bin` as a dev dependency.

### 7. Validate Code in a pre-commit git Hook with husky

`husky` works in a similar way to `ghooks`, except that you add npm scripts\
to run pre and post-hooks.

### 8. Auto-format all files and validate relevant files in a precommit script with `lint-staged`

`lint-staged` not only allows linters to be run on precommit, but allows one\
to define commands to be run should linting pass, allowing for repo owners to\
have autoformatting run and changed files staged without relying on\
collaborators to have autoformatters enabled.

## JavaScript Mocking Fundamentals

**Table of Contents**

* 1\. Override Object Properties to Mock with Monkey-patching in JavaScript
* 2\. Ensure Functions are Called Correctly with JavaScript Mocks
* 3\. Restore the Original Implementation of a Mocked JavaScript Function with jest.spyOn
* 4\. Mock a JavaScript module in a test
* 5\. Make a shared JavaScript mock module

### 1. Override Object Properties to Mock with Monkey-patching in JavaScript

[01-override-object-props-to-mock-with-monkey-patching.no-framework.js](file:///C:/Users/bryan/Downloads/01-override-object-props-to-mock-with-monkey-patching.no-framework.js)

```
$ node 01-override-object-props-to-mock-with-monkey-patching.no-framework.js

#  or
<p class="mume-header " id="or-1"></p>

$ npx jest 01
```

We import both `thumb-war` and `utils`, and mock out `utils.getWinner` so\
that we can evaluate other aspects of `thumb-war`. We are making\
`utils.getWinner` deterministic, as the randomness of what it usually returns\
will affect the outcomes of our tests in non-deterministic ways.

After mocking, one should always clean up so that subsequent tests are not\
affected by the mock.

### 2. Ensure Functions are Called Correctly with JavaScript Mocks

[02-ensure-functions-are-called-correctly-with-javascript-mocks.test.js](file:///C:/Users/bryan/Downloads/02-ensure-functions-are-called-correctly-with-javascript-mocks.test.js)

```
$ node 02-ensure-functions-are-called-correctly-with-javascript-mocks.no-framework.js

#  or
<p class="mume-header " id="or-2"></p>

$ npx jest 02
```

We can create a naive `jest.fn` which takes a mock implementation, and\
accepts all arguments passed to it.

Onto this mock function we can add a `mock` object where we can store calls\
to the mock function.

This can all be done using `jest.fn`.

Jest allows one to inspect a mocked function using `toHaveBeenCalledWith`,\
`toHaveBeenNthCalledWith`, or to inspect `mockFn.mock.calls` to inspect how\
the function was used for all calls.

### 3. Restore the Original Implementation of a Mocked JavaScript Function with jest.spyOn

[03-restore-original-implementation-of-a-mocked-function.test.js](file:///C:/Users/bryan/Downloads/03-restore-original-implementation-of-a-mocked-function.test.js)

```
$ npx jest 03

#  or
<p class="mume-header " id="or-3"></p>

$ node 03-restore-original-implementation-of-a-mocked-function.no-framework.js
```

An alternative to using jest.fn to replace a function with a mock\
implementation is to use `jest.spyOn` to create a mock implementation. This\
mitigates us having to store the original function and replace it, as Jest\
will do the heavy lifting for us.

1. Use `jest.spyOn(myObject, 'myObjectFn')`
2. Use `myObject.myObjectFn.mockImplementation(mockFn)` to create a mock\
   implementation of `myObjectFn`
3. Once done testing the implementation, use\
   `myObject.myObjectFn.mockRestore()` to replace the mock implementation with\
   the original so that other tests are not affected. `.mockRestore()`\
   restores the mock implementation to its pre-mocked definition

### 4. Mock a JavaScript module in a test

[04-mock-a-js-module-in-a-test.test.js](file:///C:/Users/bryan/Downloads/04-mock-a-js-module-in-a-test.test.js)

```
$ npx jest 04

#  or
<p class="mume-header " id="or-4"></p>

$ node 04-mock-a-js-module-in-a-test.no-framework.js
```

Using `jest.spyOn` is still a form of monkey-patching. Monkey-patching is\
great for our own modules, because we're using commonjs's `require`. When\
working in an es environment we'll have to mock an entire module, and\
`jest.mock` allows one to do this.

When using `jest.mock` we can create the mock implementation inside of the\
mock, and no longer need to use `jest.spyOn` and\
`[fnToMock].mockImplementation`.

Instead of using `mock.mockRestore()` to restore the mock implementation, we now\
use `mock.mockReset()`. `mock.mockRestore()` is only available for mocks\
created with `jest.spyOn`

`jest.mock` can be placed anywhere in the file, and Jest will hoist it to the\
top of the file.

***

`jest.mock` takes control of the entire module requiring system. This can be\
simulated using `require.cache`. `require.cache` is an object that has keys\
for each import, with each key being associated with an object containining\
information regarding the import.

We use the `exports` property to create the mock implementation of the module

### 5. Make a shared JavaScript mock module

[05-make-a-shared-mock-module.test.js](file:///C:/Users/bryan/Downloads/05-make-a-shared-mock-module.test.js)

```
$ npx jest 05

#  or
<p class="mume-header " id="or-5"></p>

$ node 05-make-a-shared-mock-module.no-framework.js
```

We can have tests automatically use the mock of a module by adding the mock\
to a `__mocks__` folder. Node modules can be placed in the `root` `__mocks__`\
that Jest inspects, by default adjacent to `node_modules`. For user-defined\
`modules` they can be placed in a `__mocks__` folder adjacent to the module.\
The mock must use the same filename as the mocked module.

For user-defined modules, if the test imports the module, then\
`jest.mock('./path/to/mock')` must be added to the test file.

***

Similarly to how we simulated what Jest is doing when conrolling module\
requiring by using `require.cache`. We create a file containing the mock,\
which also uses our `fn` to allow us to evaluate calls. In our test we import\
our mock, and then retrieve the cached paths for the actual utils and mock\
utils, rewriting `require.cache`'s key for the actual utils with the mocked\
utils.

Notes\
Node vs Browser\
A tale of Two Runtimes: Node.js vs Browser

Different browsers can differ wildly because JS is treated as a specification.\
Global vs Window

In Node.js runtime, global object is where global variables are stored.

In browsers, window objects are where global variables are stored.

We cannot reference window in node.\
Browsers have access to the document object, which contains the html for the page, node does not have this.

Browsers also have access to a location that contains information about the web address, node doesn't.

Node has the require module, that we can use to import installed modules, and export our own files.

File I/O\
The FS Module

Node ships with a File System module that contains methods that allow us to interact with our pc's FS.

require will return to us a object with many properties that will enable us to do file I/O.

The file system (fs) module contains a lot of functionality.

Offers both async and sync functions.\
Most likely covers all operations regarding files.\
Creating a New File

Write File : Method used to create an file in the FS module.\
fs.writeFile("foo.txt", "Hello world!", "utf8", (err) => {\
if (err) {\
console.log(err);\
}\
console.log("write is complete");\
});\
Arguments go as follows:\
Name of new file.\
String input.\
Code Encoding (UTF-8)\
Callback to signal end\
Since Write File is async, we need to use callback chaining to guarantee commands occur after a write completes or fails.\
Reading Existing Files

Read File : Method used to read the contents of a file.\
Arguments go as follows:

Name of new file.

Code Encoding (UTF-8)

Error Object

Callback to signal end

fs.readFile("poetry.txt", "utf8", (err, data) => {\
if (err) {\
console.log(err);\
}\
console.log("THE CONTENTS ARE:");\
console.log(data);\
});\
You can do anything you want with the contents of a read file, since it is just one giant string!\
Fancy File I/O

We can use Read File and Write file together to replace occurences of certain phrases with something else.

const fs = require("fs");

function replaceContents(file, oldStr, newStr) {\
fs.readFile(file, "utf8", (err, data) => {\
if (err) {\
console.log(err);\
}\
let newData = data.split(oldStr).join(newStr);\
writeContents(file, newData);\
});\
}

function writeContents(file, data) {\
fs.writeFile(file, data, "utf8", (err) => {\
if (err) {\
console.log(err);\
}\
console.log("done!");\
});\
}

replaceContents("poetry.txt", "do not", "should");\
Gitting Started with Git\
Version Control Systems help us keep track of changes over time.

VCS tools abstract the work of keeping projects and programmers in sync with one another.\
Git is the most popular VCS in use today.

Repository : Comprises all the source code for a particular project.

Commits : collection of changes grouped towards a shared purpose.

msg and hash

Commit Messages & Commit Hashes : Git id's your commits with a specially generated series of letters and numbers called a Hash, and a detailed message describing the commit.

They are 40 characters long, but by default Git abbreivates hashes to 7 characters.\
References : Way to alias a commit in plain english.

HEAD : default reference that points to the most recent commit.\
Git maintains three lists of changes:

Working Directory\
Keeps all of your in-progress changes.\
Staging Area\
Reserved for changes you're ready to commit.\
Commit History\
Made up of changes you've already committed.\
Tracking Changes in a Repository

Git Init : Allows you to start a repository in your current directory.

Git Add : Adds content to your repository.

Git Status : Used to check what has been staged in your repository.

Git Commit -m : Used to commit your changes/additions into the commit history - flag is used to add a message to your commit.

Git Log : Used to check your repo's commit history.

Branches and Workflow

Branch : Basically a seperate timeline in Git, reserved for it's own changes - Master is the default branch.

Git branch \[name of branch] : Used to reate a new branch. img of branch

Git Checkout : Used to switch to an existing branch.

Bringing it Back Together

Git Merge : Used to integrate one branch into another.\
Connecting with the world via Github

DCVS : Distributed Version Control System - built in support for managing code both locally and from a distant source.

We typically refer to the primary remote of a repo as the origin.

We clone a distant repo once, use pull to update it and push our own code into it.

Collaboration via Git and Github

> git branch add-my-new-file\
> git add my-new-file.js\
> git commit -m "Add new file"\
> git push -u origin add-my-new-file\
> This is the typical push workflow.

\-u flag stands for --set-upstream.

Git Pull : Used to update your files from remote repo - behind the scenes this is a combination of Git Fetch and Git Merge. git pull behind

Pull Request : Feature specific to Github, used as a safety net to prevent bugs and is a critical aspect of collaboration workflow.

Push your code to GH on it's own branch.\
Open pull request via a base branch.\
GH creates a comparison page for your new code vs existing.\
Your teammates review for errors.\
Make changes based on feedback and push new commits upwards.\
PR automatically makes changes.\
If everyone is satisfied, a repo maintainer will merge your branch.\
Your code is now on the main branch - nice!\
Managing your Github Repository\
Git Diff : Used to visualize any tracked differences in our repository. img of diff\
\--- & +++ : let's us know there are both additions & subtractions in our js file.\
@@ : shows use where our chunk of changes are line-wise.\
Diff Options

By default git diff compares the cwd to the last commit.\
If we append --staged we can compare the staging area instead of the cwd.\
There are many other ways to use git diff to compare as well!

## See differences between the 'feature'

## branch and the 'master' branch.

> git diff master feature

## Compare two different commits

> git diff 1fc345a 2e3dff

## Compare a specific file across separate commits

> git diff 1fc345a 2e3dff my-file.js\
> Time Travel checkout

Using Git Checkout is entirely non-destructive.\
Git checkout has a few special characters and reserved references.

## You can checkout a branch name.

## You'll be using this particular branch a lot!

> git checkout master

## You can also use commit hashes directly

> git checkout 7d3e2f1

## Using a hyphen instead of a hash will take

## you to the last branch you checked out

> git checkout -

## You can use "HEAD\~N" to move N commits prior

## to the current HEAD

> git checkout HEAD\~3\
> Git Do-over: Reset and Rebase\
> Git Reset : Can be used to travel back in time before a bug or error occured.\
> \--soft : appended to reset to move our HEAD ref to the commit we specified - does not touch our code, only resets commit messages.(least dangerous!)\
> \--mixed : Default state of Git reset, changes are preserved but they are removed from the commit history straight to the working directory.\
> \--hard : Adjust head ref and totally destroy any interim code changes\
> Rebase: An Alternative Form of Time Travel

Git Rebase : Involves changing your current branch's base branch.\
Rebasing is a dangerous process that involves re-writing history. diagram of rebase\
The Golden Rule of Git

Never change the history of a branch that's shared with others.

This rule refers to git reset and git rebase.\
Just don't use these on any branch that is shared with others!!!\
Git Merge Conflicts & You\
Merge Conflict : Special state that Git presents to us when two branches have code changes that are incompatible with each other. conflict img\
Conflict Resolution img of conflict

The <<< === >>> signs are delimiters to indicate we are separating two pieces of conflicting code.\
The first piece is from our Base Branch.\
The second piece is from our incoming branch.

***

***

***

### --

***

Flashcards\
Node JS

1. Name two objects that are available in browser runtimes but not in NodeJS.

Common answers include window, document, and location.\
2\. The "global object" in the Node runtime can be referenced with the name global. What name can be used in browser runtimes to reference the "global object"?

window\
3\. The 'fs' module contains synchronous and asynchronous versions of many of its methods. Why might one prefer to use the asynchronous versions of these methods?

Operations on our file system can take some time because data from our hard drive must be read or written. Performing these operations asynchronously prevents the single thread of our JavaScript program from being blocked during these operations.\
4\. What is the name of the node module that allows us to perform operations on our local file system?

fs\
5\. What is the name of the node module that allows us to take user input?

readline\
6\. What site should be used as a documentation reference for JavaScript in browser runtimes?

MDN, Mozilla Developer Network ([https://developer.mozilla.org/en-US/docs/Web/JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript))\
7\. What site should be used as a documentation reference for JavaScript in the Node runtime?

[NodeJS.org](http://nodejs.org) ([https://nodejs.org/api/](https://nodejs.org/api/))\
Git Reset

1. git reset --hard

Moves HEAD ref to\
Discards all code changes after !

DANGEROUS! Use with caution.

1. git reset --soft

Resets HEAD ref to\
Leaves changes in the staging area\
3\. git reset

Same as git reset --mixed

Moves HEAD ref to

Changes moved to working directory; must be re-git added.

1. git reset HEAD myfile.js

Removes myFile.js from the staging area

Step 1: Attempt to replicate circumstances\
In software development, the first step towards debugging any issue is attempting to replicate the circumstances. With most programming languages, this is bolstered by reviewing logs leading up to an error, but with client-side JavaScript, this type of diagnosis requires significantly more foresight (more on that below).

Before we can replicate any circumstances of an issue, and assuming we have access to any production logs, we first need to establish some testing guidelines. This involves doing things like mimicking the production database, the user accounts involved, and even the operating system. Everything is fair game here.

Step 2: Test assumptions\
Once you've established the circumstances that you think might throw the exception or error you are hunting down, it's time to test them. Never test exceptions in production. Development and staging environments are designed to be breakable without any impact on the end users, so always always always try to break your code in a safe environment.

Step 3: Increase logging\
More information is always better. Using the methods described in Where are JavaScript Errors Logged?, the first step towards diagnosing any issue is to increase the amount of data you are logging. This allows you to see everything that is happening before and after a problem occurs. There is a good chance that the problems you are experiencing have potential warnings associated with them that don't necessarily make it into the logs by default.

Step 4: Adjust test parameters and try again\
If you were unable to replicate the problem in Step 2, then it's back to the drawing board. Not every error is easy to reproduce, and may have time-based constraints or something else making it difficult to replicate in a non-production environment. Jump back to Step 1, adjust your test parameters, and try it all over again.

What are Source Maps?\
In a production environment, JavaScript files are often compressed. While this is a great way to speed up any application, it can make debugging a nightmare, as the files you are trying to debug are practically unreadable. This is where source maps come into the picture. Supported by most modern browsers, a source map is a file that maps uncompressed code to compressed code, allowing you to see exactly what is going on within a production environment.

While most minification tools have built-in support for source maps, it is important to understand exactly how the browser connects compressed code to uncompressed code. In a nutshell, this comes in the form of a comment within a compressed JavaScript file that points directly to the source map.

//# sourceMappingURL=http://example.com/path/to/your/sourcemap.map\
As an example, let’s take a look at the developer console without source maps enabled. In particular, do you see how the source file says "Generated by CoffeeScript" and there is automatically generated JavaScript below?

Developer Console Debugger

Developer Console Debugger

While the production JavaScript file can be viewed and debugged it's not the code I wrote. The original source file is written in CoffeeScript, which means that any insight gleaned from the compiled code will have to be translated back into the original source file.

Now, let’s take a look at the same example with source maps enabled:

Developer Console Debugger

Developer Console Debugger

With source maps enabled, the breakpoint in the compiled script can be mapped directly to the original source file in CoffeeScript. This makes it possible to debug issues in the context of the source files, rather than the compiled and sanitized files that make it into the production environment.

Stack Traces in JavaScript\
Whenever exceptions are thrown, a stack trace is usually included. But, what is a stack trace? In essence, it is a rundown of every file and function that is called leading up to the error. To be clear, a stack trace doesn't include the files and functions that are touched before the error occurred, only the chain of methods that are called as the error happened. This allows you to "trace" the "stack" of operations that are performed when an error happened in order to identify exactly what went wrong, and where.

As an example, let's take a look at the stack trace that is returned from the following (incredibly simplistic) code:

function do\_the\_thing() {\
throw new Error("a thing happened!");\
}\
do\_the\_thing();\
When do\_the\_thing() is executed, an exception is immediately thrown. This results in the following stack trace:

Error: a thing happened!\
Stack trace:\
do\_the\_thing@debugger eval code:2:9\
@debugger eval code:1:7\
As you can see, rather than simply returning the exception message, reading the stack trace in reverse order shows that the exception was thrown on line 1 at column 7, but was triggered by a call to do\_the\_thing() on line 2 at column 9. For more complicated stack traces, this can be invaluable as it gives us a lot of post-mortem information.

A Brief Introduction to The Debugger\
While diagnosing bugs via log files works in some cases, sometimes a little more power is required. Thankfully, in most browsers it is possible to pause running scripts and step through them in order to identify issues as they happen. While doing this, it is possible to view and edit variables and properties, which gives you total control over the state of any running script in order to identify what is happening, where, and why.

Developer Console Debugger

Developer Console Debugger

The browser-based debugger is an incredibly powerful tool, but the basic idea behind its use is that you add breakpoints at points in your codebase where you want to look deeper at the stack and scope of the script. When the debugger encounters these breakpoints, all execution is paused, and you are provided with the opportunity to review the values of any variables, and even modify them to better test assumptions and fix any issues you may be encountering.

Advanced Debugging

It is important to note that, while the built-in debugger is great for many use cases, the introduction of more advanced JavaScript platforms like Vue.js and Redux can make the debugger significantly more complicated to use. This is where browser-based plugins like Vue.js devtools and Redux DevTools come into play. These tools are designed with the complexities of their targeted platforms in mind, allowing for much more targeted debugging of highly complex codebases.

Debugging Node.js

While the browser debugger is great for client-side JavaScript, Node.js ships with its own debugger for server-side debugging. Debugging Node.js code can be accomplished by running node inspect on the source file, or through any number of great IDE integrations designed specifically for Node.js debugging.

Visual Studio Code Node.js Debugger

Visual Studio Code Node.js Debugger

Debugging Production Errors Using Rollbar\
The challenge when debugging production errors is that you do not have access to the user’s developer console. In order to gather enough context to identify the problem, you need to track information in a central location where your developers can access it. Traditionally, this is done using logs but there is a better way.

Rollbar empowers you to not only identify what is happening, but when, where, to whom, and how often. Rather than having to sort through pages of raw text logs, Rollbar aggregates data from individual exceptions and errors and creates a dashboard with as much information as possible for analyzing these issues.

When properly configured, these exceptions can be tied directly to user accounts, and tracked over time across an easy-to-read graph—with deployment markers to boot. While this doesn't necessarily tell you exactly what an issue is, it comes as close as possible to doing so by providing you with more information than you could possibly ask for.

Telemetry\
Telemetry screenshot

Unique to Rollbar's JavaScript integration, telemetry is an advanced event tracker that allows you to identify what happens in the moments leading up to, and immediately following, an error. This is incredibly valuable, as it gives you the ability to see any behavior that may have inadvertently lead to the error being thrown.
