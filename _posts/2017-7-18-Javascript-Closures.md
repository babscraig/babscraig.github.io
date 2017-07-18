---
published: false
---
Concepts you need to understand this:
- A basic understanding of Javascript
- Scope in JavaScript

Anytime a function is declared within another function in JavaScript, closure can be observed. Take a look at the example below:
```
// Global variable 'a'
var a = 1;

function foo() {
// Inner variable 'a' scoped to foo()
  var a = 2;
  
  // we declare a function bar() inside of foo()
  function bar() {
	// bar() doesn't have an 'a' variable but has access to the 'a' in foo()
    return a;
  }
  
  return bar;
}

var baz = foo();
baz();
```
In the example above, we have a global variable ```a``` with a value of 1. Then we declare a function ```foo()``` with its own ```a``` variable set to 2. Inside of ```foo()``` we then have ```bar()``` which returns the value referenced by an ```a``` variable. Finally ```foo()``` returns the inner function ```bar()```.

We store the returned function inside of a variable ```baz``` and then call the function. Which of the two 'a's do you think get printed out to the console? Instead of printing out the global 'a' variable, we instead get the inner 'a' stored in ```foo()```.

This is the magic of closures. They give us an 'imprint' of the values at the time the inner function was declared. This is also known as storing state. If you also notice, ```foo()``` has been called and run before we even reference the inner ```bar()``` function but this does not stop it from having access to the variables declared in the function.



Tidbits:
1. Anytime you declare a function in another function, you have created a closure on the variables declared in the outer function.
2. To use the closure you must 'expose' the inner function by returning it directly or setting it as a method returned from the outer object.
3. The inner object, whenever and wherever it is called will have a copy of the state of the outer function where it was declared.
4. The concept of closures, scope and lamdas are what allow us to work magic with event handlers and callbacks. Take the time to understand these three concepts fully.
