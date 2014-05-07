# Unobtrusive JavaScript

Yesterday we did some basic, old-school JavaScript. 

But there wasn't really much separation of concerns! 

We've got code in our HTML; this is a 'smell' in our code. 

We've put our code in the [global scope](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions_and_function_scope) - anyone can access our functions.

  - Which means anyone can overwrite our functions
  - Or other people can overwrite ours by accident
  
Making our JS unobtrusive means seperating it from our HTML content, and ensuring to the best of our abilities that it operates in the least surprising way across the majority of user agents (web browsers, phones, search bots...).


## Progressive enhancement vs graceful degradation

Remember how we talked about stacks (of software that works together)? You can think of the front-end of our app a bit like a stack.

- We've got HTML at the front, but we've also got CSS, and JavaScript. 

- We can use JavaScript to make our interfaces nicer, but we shouldn't rely on it. 

If we have a pure-HTML version then it will work on a lowest common denominator system, which means it'll be - more resilient. 

If we then add fancy CSS and JavaScript on top of it, we are *progressively enhancing* our app. 

The alternative is to make it as fancy and clever as we can, and then try to make it work without those things. 

- This is *graceful degradation*. 
  - This is generally harder (to work out retrospectively how to make your functionality work without JS) 
  - It's generally not as comprehensive (noscript blocks and "please use a browser with JS" messages) 
  - __Do the other way first; there's less to go wrong and it keeps your feedback cycle short.__


## Progressive enhancement

We're going to take the previous code for an onclick event, and make the JS unobtrusive.

```
  <!-- existing code -->
  <html>
  <head>
  </head>
  <body>
    <button onclick="alert('Hello WDI!');">I'm a button!</button>
  </body>
  </html>
```

Give the element an ID, remove the event from the element, and add an 'event listener':


```
<html>
<head>
  <script>
  function sayHello() { 
    alert('Hello WDI!'); 
  }
  
    var helloButton = document.getElementById('helloButton');
    helloButton.addEventListener('click', sayHello);
  </script>
</head>
<body>
  <button id="helloButton">I'm a button!</button>
</body>
</html>
```

However, this doesn't work... because the `eventListener` is being called before the document is loaded (so the helloButton element does not exist)

You will see the error `Uncaught TypeError: Cannot call method 'addEventListener' of null ` (or similar) in your console.

To solve this, the simplest solution is to move the script to the bottom of the page. This is one approach to ensure the document is loaded before running JS files. This also has the side benefit of making sure that any blockers in your JS (like alerts, or even slow api calls or slow logic) don't stop the user from seeing HTML content.

Or we can wait to execute our JS once the browsers tells us the document is finished loading.

Different browsers impliment different methods for determining this, and some JS libraries (like jQuery, which we haven't covered yet) offer simple solutions that work for the majority. But one method that works for our case (because it's only our code we're running, and we know nowhere else calls the same method) is to use `window.onload`:

```
  <script>
  function sayHello() { 
    alert('Hello WDI!'); 
  }
  
  function setup() {
    var helloButton = document.getElementById('helloButton');
      helloButton.addEventListener('click', sayHello);
    }
    
    window.onload = setup;
  </script>
```

As soon as we incorporate any other JS libraries, the `window.onload` can trample on their implementations of this 'document ready' method, or they may trample on ours.

Instead of `window.onload` we can add an event listener, either to the window, or the document... which will work for *most new* browsers:

```
document.addEventListener('DOMContentLoaded', setup);
```

or 

```
window.addEventListener('load', setup);
```


# JS Modules

As we've seen, it's possible that different JS files and libraries can override the definition of each others' functions if everything is written in 'global' scope.

We can 'scope' our functions to avoid conflicts with other people's (or other libraries') code. 

We need to instanciate a new object to tie all our functionality to; add the existing functions to properties on my scope object; and alter the calls to the functions to refer to the new scope:

```
  <script>
    var myApp = myApp || {}; 
    
    myApp.sayHello = function () { 
                       alert('Hello WDI!'); 
                     }
  
    myApp.setup = function () {
    var helloButton = document.getElementById('helloButton');
      helloButton.addEventListener('click', myApp.sayHello);
    }
    
    document.addEventListener('DOMContentLoaded', myApp.setup);
  </script>
```

The result of this is that instead of creating a bunch of things in our global scope, we create one global and put everything in it.

We can extend this, as our app grows, by creating "submodules"; other JS files that we write can access our existing scope and extend it:

```
  <script>
    var myApp = myApp || {}; 
    var myApp.subModule = myApp.subModule || {}; 
    
    myApp.subModule = function () { 
                       alert('Hello from the submodule!'); 
                      }
  </script>
```

Whether or not the extra file is included, it should not clash with other code - and if all our code follows this approach, then nothing should clash. Should...


# CSS/JS interaction

We can use manipulation of CSS to manage the display to the user in the event of JS not being enabled. Essentially, if JS *is* enabled, we will remove the CSS class that controls the hiding of buttons and the display of the warning message.



```
  <html>
  <head>
    <style>
      /* stuff that happens if JS is enabled (ie. _don't_ show the "you've not got js" warning) */
      .nojs_warning {
        display: none;
      }
      
      /* stuff that happens if JS is disabled (ie. show the "you've not got js" warning, and hide all the buttons that have js events) */
      .nojs button {
        display: none;  
      }
      .nojs .nojs_warning {
        display: block;
      }
    </style>
  
    <script>
      var myApp = myApp || {}; 
    
      myApp.sayHello = function () { 
                         alert('Hello WDI!'); 
                       }
  
      myApp.setup = function () {
      var helloButton = document.getElementById('helloButton');
        helloButton.addEventListener('click', myApp.sayHello);
      
        var body = document.getElementById('body');
        body.className = body.className.replace(/nojs/, '');
      }
    
      document.addEventListener('DOMContentLoaded', myApp.setup);
    </script>
  </head>
  <body class="nojs" id="body">
    <button id="helloButton">I'm a button with JS event!</button>
    <p class="nojs_warning">You need JS to view this site best...</p>
  </body>
  </html>
```

Instead of putting the class on the body, you could put it on all the elements concerned... the detail of the implimentation is your choice :-)




# Closures

Fewer global variables is "better". So far we've managed to reduce our pollution of the global scope to just one variable housing our app's module.

- But we can also do things that don't have ANYTHING in the global scope.
- Remember our anonymous functions from yesterday? 
  - What if we invoked them immediately?
  
```
  var bar = (function() { 
    var i = 0; 
    return { 
      foo: function() { 
        console.log(i);
        i++;
      }
    };
  })();
```

  - We can use this to keep stuff completely hidden from the global scope.
  - Don't worry too much about how this works; just be aware of it, as you may see it in the real world.


To remove our "myApp" object from global scope, we can wrap it in a closure - everything works just as it did before, but now, the browser console has no idea about the `myApp` object.

```
 <script>
    (function () {
      var myApp = myApp || {}; 
    
      myApp.sayHello = function () { 
                         alert('Hello WDI!'); 
                       }
  
      myApp.setup = function () {
      var helloButton = document.getElementById('helloButton');
        helloButton.addEventListener('click', myApp.sayHello);
      }
    
      document.addEventListener('DOMContentLoaded', myApp.setup);
    })();
  </script> 
```