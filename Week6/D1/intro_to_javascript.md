# Intro to JavaScript



### Intro

- What programming languages have we seen so far? 
  - Ruby
  - HTML (Markup)
  - CSS (Nope)
  - SQL (Query)
  - erb (Kinda? Markup and programming, but Ruby)
  - YAML (Markup)
- Today we're doing JavaScript. 
  - Just another programming language, and you've learnt programming so far, not just Ruby
  - But it's going to look very different. 
- Why do we use JavaScript? 
  - Generally for front-end dev (which means it runs on the browser) 
  - No real reason why, except that Netscape wanted something Java-like and Ruby was unknown at the time. 


### Differences 

- Let's add two numbers together
  
  ```
  function add(a, b) { 
    return a + b;
  }
  ```

  - `function`, not `def`
  - We have to explicitly `return`, it doesn't happen automatically. If we don't return explicitly, the return value of a function will be `undefined`.
  - We need a semicolon at the end of the line. 
  - We need brackets to indicate the start and end of the function.
  - We need parentheses around the method arguments. 

- Let's reverse a string [1]. 

  ```
  function reverseString(string) {
    var result = "";
    for (var i = string.length-1; i >= 0; i--) { 
      result += string[i];
    }
    return result;
  }
  ```
  
  - JS naming convention is to have camelCase method and variable names.
  - The loop syntax, is different to what we're familiar with in Ruby. There are three parts of a JS loop:
      - There's a "this is the starting condition"
      - "this is the situation that will cause it to end"
      - "this is what to do " 
  - Variables are defined with 'var' before them - if the 'var' is missing, then the variable scope could be 'hoisted' [3] and strange results can ensue...
  - But we can still use += to add strings together. 
  - And we can still get access to characters of a string, we can still use `string[i]`. 
  - We comment with `//` for single lines or `/* comments here */` for multiline comments.

  - We can output to the console using `console.log('Stuff to say in console')`, and the contents of the log message can be anything that can be 'stringified'. Logging to the console in JS is similar to 

  ```
  function stringChars(string) {
    for (var 1 = 0; i < string.length; I++) {
      console.log("The character at position " + i + " is " + string[i]);
    }
  }
  ```

  - if you call a function in JS that doesn't have any arguments, you still need the brackets to execute the function.
  
    `"my string".toUpperCase()`
  
    Without the brackets, the return value would be the function itself.



### Let's jump in

- When we write JS we generally do it in a browser. 
- The browser is not JavaScript. You can write JavaScript outside of a browser. We're not going to though. 


- Create a JS file with an alert in it
- Create an HTML file that loads it

```
<!doctype html>
<html>
  <head>
    <title>My First JS App</title>
  </head>
  <body>
    <h1>This will be my first JS App!</h1>
  </body>
</html>

```

You can include JS in script tags in the source, and console logs appear in the browser console.

```  
  <script>
  function add(a, b) {
    a + b;
  }
  console.log(add(6, 9));
  </script>
```

or in a separate file.

```
  <script src="application.js"></script>
```

We can interact with the user by using the `alert` function:

```  
function add(a, b) {
  a + b;
}
alert("my number is " + add(6, 9));
```

Or `prompt` - in the next exercise...

  


### Hello JS World. 
- Let's ask for 5 items, and then reverse them. 

```
var items = [];
for (var i = 0; i < 5; i++) { 
  items.push(prompt("Please enter item " + i));
}
alert("Your items are: " + items.join(", "));
```

- Ask for two numbers, and add them.




### Watch out! 

- JS will *coerce* things for you, like duck typing, but it'll not always do what you think it should...

  `30 - 7 // 30`
  `"37" - 7 // 30`
  `"37" + 7 // "377"`
  `parseInt("37") + 7 // 44`
  `parseInt("foo") + 7 // NaN`
  
  Some of these quirks may be addressed in future versions of JS [2], but there are so many inplementations in so many historical browsers, that changes to the implementation would break code that is written to take advantages of 
    

- JS has null instead of nil... but it also has undefined. 
  
  ```
  var a = null;
  a + 2 // 2, because null -> 0
  var b; // undefined;
  b + 2 // NaN
  ```

- Some things in JS are falsey that are truthy in Ruby (which also shows us JS `if...else...end` constructs). 

  ```
  if (true) {
    alert("truthy!");
  }
  
  if (false) { 
    alert("False is truthy?");
  } else { 
    alert("False is not truthy!");
  }
  
  if (0) {
    alert("number 0 is truthy!");
  }
  
  if ("") { 
    alert("A blank string is truthy!");
  }
  
  if ("0") { 
    alert("The string 0 is truthy!");
  }
  ```
  
### Some other looping

As well as `for` loops, you can loop with `while`, and you can use any truthy evaluation for comparison.

```
var i =o;
var found = false;

while (!found && i < items.length) {
  if (items[i] == 'magic bag') {
    found = true;
  }
  i++;
}

if (found) {
  console.log("user has a magic bag");
} else {
  console.log("user just has regular stuff");
} 
```


### Case statements


```
var whatever = 'add';

switch (whatever) {
  case 'add':
    alert('5 + 7 = 12');
    break;
  case 'subtract':
    alert('6 - 2 = 4');
    break;
  default:
    alert('this is the default action');
  }
```

Without the `break` statements, the conditions will cascade through.


### Exception catching

```
try {
  // something might error
  var num = prompt('Enter a number between 1 and 100:');
  if (num < 1 || num > 100) {
    throw('Number out of bounds');
  }
  console.log('your number was ' + num);
} catch (error) {
  // my error is now called
  console.warn('Something went wrong: ' + error);
} finally {
  alert('I always run, errors or not');
}
```

aside: beware of differences between languages in what constitutes errors (like division by zero), and what doesn't. In JS, `1 / 0` => `infinity`


### Object literals

- Ruby had hashes. JS doesn't have hashes. 
- JS has objects. You've already used them. 
  - JSON == JavaScript Object Notation.
  
  ```
  var foo = {name: 'Alex', age: 21 };
  ```
  
  - This looks a bit like a (Ruby) hash, doesn't it? 
  - JS objects are a collection of name/value pairs. But some of those values can be functions. 

  ``` 
  function foo() { 
    alert("I'm foo!");
    }
    
  bar = function() { 
    alert("I'm bar!");
  }
  
  foo();
  bar();
  ```
  
  These two ways of defining functions in JS are very common. The "anonymous" approach is like our blocks/procs in Ruby.

- We don't have to create classes before we create objects... we can create object literals.
  ```
  cat = {
    name: "Fluffy", 
    sound: "Miaow", 
    talk: function() { 
      alert(this.sound + ", " + this.sound);
    }
  };
  
  cat.name; // "Fluffy"
  cat.sound; // "Miaow"
  cat.talk();
  ```
  
  In our JS objects, the 'keys' are referred to as 'properties' (because they're not key:value pairs (hashes), they're 'ojects').

- But we can create functions that return objects... (and the convention for functions that return object is the name them CapitalCamelCase )

  ```
  function Cat(name) { 
    this.name = name;
    this.sound = "Miaow";
    this.talk = function() { 
      alert(this.sound + ", " + this.sound);
    };
  }
  
  mittens = new Cat("Mittens");
  maru = new Cat("Maru");
  mittens.talk();
  mittens.name;
  maru.talk();
  maru.name;
  ```



# The DOM and the Web Browser

- As I said before, JavaScript is not the same as the web browser. And the web browser is not the same as JavaScript. You can run your JavaScript code without a web browser. But it is a pain. 
- And generally, we want to write stuff in the browser. 
- We do this via the DOM. We've seen this a little bit in 'Inspect Element', and seen our elements as trees. But the DOM is also what lets our code manipulate stuff in the browser. 
- The DOM lets us do two things: manipulate the document, and run code when things happen. 

### Event driven programming

- We've written a couple of different kinds of programs so far. Imperative ones, and object oriented ones. 
  - Imperative code that starts at the top line, and chugs down through it until it finishes. 
  - Object oriented programs, where we define a bunch of objects and then our code jumps around as necessary. 
- But we've also written event-driven programs, though we haven't called it that. 
  - When our web server runs, it sets up our app and then just sits there. 
  - It's not until something happens - an event - our visit to the web page - that our code runs. 
- JS is a bit like that. 


We can define events on elements, and what JS to run when the event happens. `onclick` is a very common event in web browsers:

```
<html>
<head>
</head>
<body>
  <button onclick="alert('Hello WDI!');">I'm a button!</button>
</body>
</html>
```

So is `onmouseover`, so we can use the event handler for that event:

```
<img src="normal.jpg" onmouseover="this.setAttribute('src', 'mouseover.jpg')" onmouseout="this.setAttribute('src', 'normal.jpg')" width="200" />
```

In the context of the event, `this` means the element that the event is handled for (so the image tag in this example). And any document element can have events bound to it, and we can always use the console to see what's going on.

```
<body onlick="console.log(this)">
```

### Some more event handling



```
  <script>
    // Rotate the colours whenever we click a button. 
    var colours = ["red", "orange", "yellow", "green", "blue", "purple", "silver"];
    var count = 0;
    function rotate() { 
      var listItems = document.getElementsByTagName('li');
      for (var i = 0; i < colours.length; i++) { 
        var listItem = listItems[i];
        listItem.setAttribute('style', 'background-color: ' + colours[(i+count)% colours.length]);
      }
      count++;
    }
  </script>
  <ul>
    <li>First</li>
    <li>Second</li>
    <li>Third</li>
    <li>Fourth</li>
    <li>Fifth</li>
    <li>Sixth</li>
    <li>Seventh</li>
  </ul>
  <button onclick="rotate()">Disco!</button>
```




```
  <p id="greeting">Hello!</p>
  <input type="text" id="name" onchange="update()" />
  <script>
    function update() {
      var caption = document.getElementById('greeting');
      var input = document.getElementById('name').value;
      if (input) { 
        caption.innerHTML = "Hello, " + input + "!";
      } else { 
        caption.innerHTML = "Hello!";
      }

    }
  </script>
```


If we change this from 'onchange' to 'onkeydown', it changes the behaviour that fires the event. It's still not quite the behaviour we want in this instance... so maybe 'onkeyup' instead?



-----------------------------


#FINI


### Inheritance
 
- With some languages, like Ruby, we say they have *classical inheritance*. It's not about being *classic*; it's about being *class*-based. We create objects, which are instances of a class. 
- JavaScript doesn't have Classical inheritance. It has *prototypal inheritance*. Our objects inherit directly from other objects. 


        

### Resources 

- MDN: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference?redirectlocale=en-US&redirectslug=JavaScript%2FReference
- Mozilla Developer Network JavaScript Guide: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide
- Mozilla Developer Network JavaScript DOM Guide: https://developer.mozilla.org/en-US/docs/DOM
- JavaScript puzzlers: http://javascript-puzzlers.herokuapp.com

[1]: We'll use the console in Chrome as a playground for JS - just like we used IRB for Ruby.

[2]: http://en.wikipedia.org/wiki/ECMAScript

[3]: http://code.tutsplus.com/tutorials/quick-tip-javascript-hoisting-explained--net-15092 - other resources exist that explain variable hoisting



