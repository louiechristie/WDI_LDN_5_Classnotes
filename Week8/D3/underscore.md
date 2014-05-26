# Underscore

http://underscorejs.org

http://underscorejs.org/underscore.js

http://cdnjs.cloudflare.com/ajax/libs/underscore.js/1.4.4/underscore-min.js


Underscore is a utility-belt library for JavaScript that provides a lot of functional programming support that you would expect (as developers coming from Ruby), but without extending any of the built-in JavaScript objects.

Underscore provides 80-odd functions that support familiar functional suspects: `map`, `select`, `inject` as well as more specialized helpers. It delegates to built-in functions, if present, so modern browsers will use the native implementations of `forEach`, `map`, `reduce`, `filter`, `every`, `some` and `indexOf`.

The current version of JavaScript is fairly barebones when it comes to utility functions. And Underscore.js is a nifty little JavaScript library that brings in a this functionality in a small file size.


## Getting Started

It's not a big set up process. Just grab the source, include it in your page; or link to a CDN.

Underscore creates and exposes all its functionality via a single object, in global scope. This object is the titular underscore character, `_.`

Yes, this is similar to how jQuery works with the dollar `$` symbol. And just like jQuery, you can remap this character in case you run into conflicts.


## Underscore in Action

Let's take a look at Underscore in action.

Assume you have a random array of test scores and you need a list of those with 90+ score:

```
  var scores = [84, 99, 91, 65, 87, 55, 72, 68, 95, 42], topScorers = [];
  for (i=0; i<=scores.length; i++)
  {
     if (scores[i] > 90)
     {
        topScorers.push(scores[i]);
     }
  }
```

It's pretty simple, but fairly verbose for what we're trying to do.

How would we do this in Ruby?...


How it's done with Underscore:

```
  var scores = [84, 99, 91, 65, 87, 55, 72, 68, 95, 42], topScorers = [];
  topScorers = _.select(scores, function(score){ return score > 90;});
```

## What do we need this for?

If your use of JavaScript is limited to merely manipulating the DOM, then the answer is mostly "nothing" since jQuery does most of what you'd want to do.

On the other hand, if you're dealing with non-DOM code or even complex (think MVC) front end code, Underscore is a boon.

Some of the functionality of the library is making its way into the ECMA specifications, it's not available in all browsers and making your code work cross browser is another nightmare on its own. Underscore provides you with a nice set of abstractions that work everywhere.

Also, Underscore falls back to native implementations, if available, to make sure performance is as optimal as possible.


## Syntax?

Let's take our earlier code as an example:
```
  var scores = [84, 99, 91, 65, 87, 55, 72, 68, 95, 42], topScorers = [], scoreLimit = 90;
  topScorers = _.select(scores, function(score){ return score > scoreLimit;});
```

This method is the functional, or procedural, approach (although the definition of "functional" is a bit of a stretched). You can also use an object oriented approach:
```
  var scores = [84, 99, 91, 65, 87, 55, 72, 68, 95, 42], topScorers = [], scoreLimit = 90;
  topScorers = _(scores).select(function(score){ return score > scoreLimit;});
```

There is no real 'right' way to do things but keep in mind that you can jQuery-esque method chaining with the latter method.


## Checking Out the Functionality

Underscore provides a little more than 60 functions that span a number of functionalities. At their core, they can be classified into groups of functions that operate on:

- Collections
- Arrays
- Objects
- Functions
- Utilities

Let's take a look at what each does and one or two examples from each section.


## Collections

A collection is generally an array of objects, an "associative array" in JavaScript - kind of equivalent to a hash in Ruby.

Underscore provides a lot of methods that operate on collections. We saw the 'select' method earlier. Here are a few more.


### `pluck()`

Let's say you have an array containing key value pairs and you'd like to extract just a specific property from each:
```
  var authors = [{name : 'Asimov', niche : 'Sci Fi'}, {name : 'Rowling', niche : 'pulp'}, {name : 'Heinlein', niche : 'Social commentary'}, {name : 'Egan', niche : 'Quantum mechanics'}];
  var niches = _.pluck(authors, 'niche');

  // ["Sci Fi", "pulp", "Social commentary", "Quantum mechanics"]
```
Using pluck is as simple as passing in the target object or array as well as which property to pick out. Here, I'm merely extracting the niche for each site.


### `map()`

Map creates an array from a collection where each element can be mutated or otherwise changed through a function.

Let's take the earlier example and extend it a bit.
```
  var authors = [{name : 'Asimov', niche : 'Sci Fi'}, {name : 'Rowling', niche : 'pulp'}, {name : 'Heinlein', niche : 'Social commentary'}, {name : 'Egan', niche : 'Quantum mechanics'}];

  var names = _(authors).pluck('name').map(function (value){return value + ' :-)'});

  // ["Asimov :-)", "Rowling :-)", "Heinlein :-)", "Egan :-)"]
```
You're not limited to simple concatenation here. You're free to modify the passed value however you wish.


### `all()`

`all()` is useful if you need to check every value in a collection passes a certain criteria. To check whether a student has passed in every subject, for example.
```
  var Scores = [95, 82, 98, 78, 65];
  var hasPassed = _(Scores).all(function (value){return value > 50; });

  // true
```

## Arrays

Underscore has a bunch of functions that work exclusively on arrays which is highly welcome since, compared to other languages, JavaScript provides awfully few methods dealing with arrays.


### `uniq()`

This method removes all duplicate elements providing you with only unique elements:

```
  var uniqTest = _.uniq([1,5,4,4,5,2,1,1,3,2,2,3,4,1]);
  // [1, 5, 4, 2, 3]
```

Only the first instance of an element is counted so the original order is kept.


### `range()`

Lets you create a 'range', or list, of numbers:
```
  var tens = _.range(0, 100, 10);

  console.log(tens);

  // [0, 10, 20, 30, 40, 50, 60, 70, 80, 90]
```
The method's parameters are, in order, starting value, ending value and step value. Using a negative step value leads to a decrementing range.


### `intersection()`

This method compares two arrays to each others and returns the list of elements that are found in all of the passed arrays i.e. an intersection in set theory.
Let's extend the earlier example to see how this works.
```
  var tens = _.range(0, 100, 10), eights = _.range(0, 100, 8), fives = _.range(0, 100, 5);
  var common = _.intersection(tens, eights, fives );

  // [0, 40, 80]
```
This will make the "MTA on Javascript" app easy to write...


## Objects

In addition to the fairly expected is checks, Underscore provides various methods to clone, extend and other manipulate objects.


### Keys and Values

Have a massive object where you need only the keys or only the values?
```
  var authors = [{name : 'Asimov', niche : 'Sci Fi'}, {name : 'Rowling', niche : 'pulp'}, {name : 'Heinlein', niche : 'Social commentary'}, {name : 'Egan', niche : 'Quantum mechanics'}];
  var keys = _.keys(authors), values = _.values(authors);

  // Asimov,Rowling,Heinlein,Egan
  // Sci Fi,pulp,Social Commentary,Quantum mechanics
```

### `defaults()`

This method is quite useful when you need to create objects with sensible defaults when one might not be used when creating it.
```
  var cars = { Make : 'Ford'};
  var defaults = { Make : 'Please Specify', Model: 'Please Specify', BodyType: 'Saloon'};
  _.defaults(cars, defaults);

  // Object {Make: "Ford", Model: "Please Specify", BodyType: "Saloon"}
```
## Functions

As wonky as it sounds, Underscore has functions that work on functions. Most of the functions tend to be fairly complicated to explain here, so we'll take a look at the simplest.


### `bind()`

this is an elusive part of JavaScript and tends to leave a lot of developers really confused. This method seeks to make it a bit easier to tackle.
```
  var o = { greeting: "Howdy" },
    f = function(name) { return this.greeting +" "+ name; };

  var greet = _.bind(f, o);

  greet("Jess")
```
It's a bit confusing so stay with me here. The bind functions basically lets you keep the value of this whenever and wherever the function is called.

This is specially useful when you're working with event handlers where `this` is hijacked.


## Utilities

And to further sweeten the deal, Underscore provides a ton of utility functions. Since we're fairly out of time, let's just look at the biggie.


### Templating

There are already a ton of templating solutions out there but Underscore makes its solution worth a look by being fairly tiny in its implementation whilst being fairly powerful.

Let's take a look at a quick example.
```
  var data = {site: 'GAmes'}, template = 'Welcome! You are at <%= site %>';
  var parsedTemplate = _.template(template, data );

  // Welcome! You are at GAmes
```
First up, we create the data to populate the template followed by the template itself. By default, Underscore uses ERB style delimiters though this is entirely customizable.

With those in place, we can simply call the template passing in our template and the data. We store the result in a separate string to be used later to update content, as needed.

Keep in mind that this is an extremely simple demonstration of Underscore's templating. You can use any JavaScript code inside the template using the `<% %>` delimiters. When you need to iterate over complex objects, say JSON sources, you can pair up with Underscore's excellent collection functions to create templates rapidly.


## Still Not Convinced?

jQuery and Underscore complement each other well and go hand in hand.

jQuery does a few things extremely well. DOM manipulation and animation are chief amongst those. It doesn't deal with anything in the higher or lower levels. If frameworks like Backbone, Ember, or Knockout deal with the higher level issues, Underscore tackles all the relatively bare metal ones.

Also, jQuery has little purpose outside the browser as the bulk of its functionality deals with the DOM. Underscore, on the other hand, can be used on the browser or on the server side without any issues. In fact, Underscore has the most number of Node modules dependent on it.


# practice

`python -m SimpleHTTPServer`


> https://github.com/Pavling/wdi-w6d3-underscore