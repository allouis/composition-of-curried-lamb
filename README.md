# On The Composition
# Of Curried Lamb(das)

Welcome, I'm Fabien AKA allouis on the internet.


# What's a Lambda ?

This one is simple.

A lambda is just an anonymous function, we use them all the time.

```javascript
doSomethingAsync(someParam, function (error, result) {
  // this is a lambda
});

doSomethingAsync(someParam).then(function(result){
  // this is a lambda
})
```


# What is currying?

Currying is taking a function that takes more than one argument and transforming it into
a function that takes a single argument. 

This new function will keep returning similar arguments until the original number of aruments
have been passed - at this point the original function is invoked with all arguments passed
to previous functions in the "curry stack"

```javascript
add5Numbers(1, 2, 3, 4, 5); // => 15;

add5NumbersCurried(1)(2)(3)(4)(5); // => 15

add4NumbersTo10Curried = add5NumbersCurried(10);

add4NumbersTo10Curried(1)(1)(1)(1); // => 14
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
like so

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

The rules of executing once all arguments are recieved still apply.

```javascript
add5NumbersAutocurried(1)(2)(3)(4)(5); // => 15

add5NumbersAutocurried(1, 2, 3, 4, 5); // => 15

add5NumbersAutocurried(1, 2, 3)(4, 5); // => 15
```


# Writing functions to be curried

You *can* curry any function you wish - provided it takes more than one argument.

However for functions that take data and act upon it, it is often more useful to
have the data as the last argument to the function.

Lets take the map function for example
```javascript

  var map = curry(function (array, func) {
    return array.map(func);
  }); 

  var oneToFive = [1,2,3,4,5];

  map(oneToFive, function (num) {
    return num % 2 
  }); // => [1, 3, 5]

  // We can curry the map function with the array

  var map1To5 = map(oneToFive);

  map1To5(function (num) {
    return num % 2  
  }); // => [1, 3, 5]

  // But this isn't extremely useful

```


# Data last

Continuing from the last example we'll rewrite the map function to take data last

```javascript

  var map = curry(function (func, array) {
    return array.map(func);  
  });

  var oneToFive = [1,2,3,4,5];

  map(function (num) {
    return num % 2 
  }, oneToFive); // => [1, 3, 5]

  // let's curry the map function with a function

  var mapOdds = map(function (num) {
    return num % 2
  });

  // and boom we can now get the odd elements in any array.

  mapOdds(oneToFive) // => [1, 3, 5]
  mapOdds([1, 23, 24, 68, 56, 99]) // => [1, 23, 99]

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

Note that the functions are executed from right to left, this may seem odd at first but it keeps
them in the same order you're to write them in. e.g

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

Granted, we have to define two extra functions for the second version.

However both these functions will be smaller than the original, and reusable.
If we have to format the username somewhere else we now have a utility for that.


# Bringing it all together.
