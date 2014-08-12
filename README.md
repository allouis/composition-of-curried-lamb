# On The Composition
# Of Curried Lamb(das)

Welcome, I'm Fabien AKA allouis on the internet.


# What are you doing?

Blah blah blah


# What's a Lambda ?

This one is simple.

Lambda is just an anonymous function, we use them all the time.

```javascript
doSomethingAsync(someParam, function (error, result) {
  // this is a lambda
});

doSomethingAsync(someParam).then(function(result){
  // this is a lambda
})
```


# What is currying?

To curry a function is to transform a function which takes `n` arguments (where n > 1)
into a function which takes one argument and returns a function which takes one argument
and returns a function, and so on and so forth `n` times.

```javascript
add5Numbers(1, 2, 3, 4, 5); // => 15;

add5NumbersCurried(1)(2)(3)(4)(5); // => 15

add4NumbersTo10Curried = add5NumbersCurried(10);

add4NumbersTo10Curried(1)(1)(1)(1); // => 14
```


# Autocurry

Auto curry is a nice lil alternative to curry, which returns a somewhat curried function
however the function can take more than one arguments and is evalutated once all the
arguments have been passed.

```javascript
add5NumbersAutocurried(1)(2)(3)(4)(5); // => 15
add5NumbersAutocurried(1, 2, 3, 4, 5); // => 15
add5NumbersAutocurried(1, 2, 3)(4, 5); // => 15
```


