# On The Composition
# Of Curried Lamb(das)

Welcome, I'm Fabien AKA allouis[_]{0,2} on the internet.


# What's a Lambda ?

This one is simple.

A lambda is just an anonymous function.

```javascript
  doSomethingAsync(someParam, function (error, result) {
    // this is a lambda
  });

  doSomethingAsync(someParam).then(function(result){
    // this is a lambda
  });
```


# Functions

## Names

When we say that a lambda is an anonymous function what we really mean, is that it is an unnamed
function.

Functions in javascript have a property called name that can only be set by using a name after the function
keyword.

```javascript
  var base = function base () {
    // .. blah blah
  };

  base.name; // => 'base'

  var whatever = function () {
    / ... blah blah blah
  };

  whatever.name; // => ''
```

.
Named functions can be handy, especially when debugging code, modern javascript interpreters, such as the V8
engine (blink, webkit, node), use the function name when showing the callstack.


# Functions

## Length

Functions in javascript also have a length property, which is going to be important when explaining and defining
the curry function.

The length property in javascript refers to the number of named arguments that the function has, you could say
that it is a property of the function signature.

```javascript
  var boom = function (a, b, c) {
    // meh
  };
  boom.length; // => 3;
```


# What is currying?

Currying is taking a function that takes more than one argument and transforming it into
a function that takes a single argument. 

This new function will keep returning similar functions until the original number of arguments
have been passed - at this point the original function is invoked with all arguments passed
to previous functions in the "curry stack"

```javascript
  add3Numbers(1, 2, 3); // => 6;

  add3NumbersCurried(1)(2)(3); // => 6

  add2NumbersTo10Curried = add3NumbersCurried(10);

  add2NumbersTo10Curried(1)(1); // => 12
```


# Why curry?

Currying can be very useful for a number of reasons: for one, it can reduce duplication of code

Take for example this piece of code

```javascript
  notify('error', 'Uh oh summin has gone wrong');

  // ... later

  notify('error', 'Sorry we couldn\'t parse that data'); 

  // ... and again

  notify('error', 'Nu uh you don\'t have correct access rights');
```


# Why curry?

If we were to have curried the notify function we could have reduced the repetitive first argument
like so:

```javascript
  var error = notify('error');

  error('Uh oh summin has gone wrong');

  // ... later

  error('Sorry we couldn\'t parse that data'); 

  // ... and again

  error('Nu uh you don\'t have correct access rights');
```


# Autocurrying

Autocurrying is an alternative to curry, it still transforms a function just like curry.

It allows you to pass any number of arguments rather than just one.

The rules of executing once all arguments are received still apply.

```javascript
  add5NumbersAutocurried(1)(2)(3)(4)(5); // => 15

  add5NumbersAutocurried(1, 2, 3, 4, 5); // => 15

  add5NumbersAutocurried(1, 2, 3)(4, 5); // => 15
```


# Advocation of autocurry

For the rest of this talk, when I use the `curry` function or talk of a curried function
I'll be referring to an auto curried function.

When starting out with using curried functions in your code, I would recommend using
autocurry as you're able to apply this to every single function in your codebase and have it
still work as expected.

After this you can then begin to effectively curry functions for reuse and readability.


# Writing functions to be curried

You *can* curry any function you wish.

However for functions that take data and act upon it, it is often more useful to
have the data as the last argument to the function, rather than data first.

The error of data first is especially prominent in array functions and propagated by libraries
such as underscore and LoDash


# Data first

Here we have the filter function, with a data first function signature.

```javascript
  var filter = curry(function (array, func) {
    return array.filter(func);
  }); 

  var oneToFive = [1,2,3,4,5];

  filter(oneToFive, function (num) {
    return num % 2 
  }); // => [1, 3, 5]

  // We can curry the filter function with the array

  var filter1To5 = filter(oneToFive);

  filter1To5(function (num) {
    return num % 2  
  }); // => [1, 3, 5]
```


# Data last

And again, the filter function, but with a data last function signature.

```javascript
  var filter = curry(function (func, array) {
    return array.filter(func);  
  });

  var oneToFive = [1,2,3,4,5];

  filter(function (num) {
    return num % 2 
  }, oneToFive); // => [1, 3, 5]

  // let's curry the filter function with a function

  var filterOdds = filter(function (num) {
    return num % 2
  });

  // and boom we can now get the odd elements in any array.

  filterOdds(oneToFive) // => [1, 3, 5]
  filterOdds([1, 23, 24, 68, 56, 99]) // => [1, 23, 99]
```


# But I need data first

On the `odd` (seldom?) occasion that you really do need to continuously filter the shit out of an array,
we can define a reverse function that will allow you to reverse the order of arguments for any function.

usage would be as such:

```javascript
  var divide = function (a, b) {
    return a / b;
  };

  var reversedDivide = reverse(divide);

  divide(4, 2); // => 2

  reversedDivide(4, 2); // => 0.5
```


# Composition

Function composition is merging multiple functions into one.
The way we do this is by passing the return value of one function as the argument to another

Here we're using the pattern of passing the return value of `add2` as the argument to `times5`

```javascript
  var add2 = function (x) {
    return x + 2;
  };

  var times5 = function (x) {
    return x * 5;
  };

  times5(add2(3)); // => 25
```


# Let's Compose

Below we right the same as the last slide, but using the compose function to glue these together

add2Times5 is now reusable

```javascript
  var add2 = function (x) {
    return x + 2;  
  };

  var times5 = function (x) {
    return x * 5;
  };

  var add2Times5 = compose(times5, add2);

  add2Times5(3); // => 25
```

.
Note that the functions are executed from right to left, this may seem odd at first but it keeps
them in the same order that they would be in had you done it manually. e.g

```javascript
  // h, g, f             and again
  h(g(f(x))) === compose(h, g, f)(x)
```


# Why compose? 

Composition of functions will usually remove duplicate code from your codebase.

Trying to create functions via composition will help you write smaller and purer functions
resulting in code that is easier to test and grasp.

```javascript
  var greetUser = function (id) {
    var user = getUserById(id);
    var name = user.name[0].toUpperCase() + user.name.toLowerCase().slice(1);
    var message = 'Hello, ' + name + '! Welcome to superapp3000';
    displayPopup(message);
  };
  // vs.
  var greetUser = compose(displayPopup, createMessage, formatUsername, getUserById);
```

.
Granted, we have to define two extra functions for the second version.

However both these functions will be smaller than the original, and reusable.
If we have to format the username somewhere else we now have a utility for that.

In fact depending on how we can compose we can now use the greetUser in two environments.

```javascript
  var log = console.log.bind(console);

  var createMessageForUser = compose(createMessage, formatUsername, getUserById);

  var greetUser = compose(displayPopup, createMessageForUser);

  var greetUserViaCommandLine = compose(log, createMessageForUser);
```


# Implementations

Let's take a look at the implementations of the curry and compose functions, these aren't super
important when developing, as you can just use a premade one (there's plenty), however it's always
good to know what's going on under the hood.



# Bringing it all together.

Now we've got through all that we can take a look at some more practical uses of these techniques, as well 
as some very useful functions that can make developing via composition a lot easier.



