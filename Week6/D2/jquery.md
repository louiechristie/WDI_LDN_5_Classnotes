# jQuery
 

## Isn't all of this JS malarkey a ballache? 

We've been writing all our own JS, but we've been discovering limitations.

- `document.getElementById('elementId')` is kind of a pain, isn't it? And so are the other "get element(s)" functionality.
- What happens if we open our page in IE? 
  - addEventListener isn't supported in browsers older than IE8. 
  - Some older browsers insist on the last parameter of the addEventListener being supplied
  - who knows what new browsers will do...


## And these are two reasons we use jQuery

- jQuery is a library that makes JavaScript nicer to work with. 
  - Smooths out cross-browser inconsistencies
  - Makes it much nicer to get access to DOM elements
  - Provides utilities for common tasks, like AJAX and some pretty stuff. 
  
  
[Get jQuery](www.jquery.com/download) and include it in your HTML.

```
  <script src="jquery-1.11.0.js"></script>
```

## jQuery basics
- The `$` is your friend. It's the jQuery Swiss Army Knife. 
  - jQuery has defined a function called `$` which returns the jQuery object
  - You'll use it to get elements to work with.
  - And to get jQuery objects, which support more methods than regular DOM objects. 
  - In Ruby, using a '$' implies global scope, and is generally considered to be 'bad' - frequently JS variables with are jQuery objects get named `$myVariableName` to indicate that they are jQuery objects (although the '$' character has no explicit special meaning in JS)
- You'll also use [http://api.qjuery.com](http://api.qjuery.com) a lot. A LOT. 


# Let's convert our example to use some jQuery

### Document Selectors

When I pass the `$` an argument, it queries the DOM.

`$('body')` will return all the DOM elements that have the tag name 'body'

`$('#navigation')` will return the DOM element that has the id 'navigation'

`$('.active')` will return the DOM elements that have the class 'active'

`$('li').each(function(i, el) { console.log($(el).text()); });` will iterate every 'li' element, turn it into a jQuery object, and output the text value to the console.


### Content modification

As we saw in the selectors, we can pluck objects out of the DOM and turn them into jQuery objects (to perform more jQuery functionality with them), and we can use jQuery to interrogate them

```
  var firstLi = $('li')[0]
  var $firstLi = $(firstLi)
  $firstLi.text()  
```

We can also use jQuery to manipulate them... to change their attributes, such as their value:

```
  $firstLi.text('new text')
```

...or change their value and add a class...

```
  $('li').each(function(i, el) { $(el).text('Hello').addClass('modified'); });
```


### Event handlers

jQuery gives us a cross-browser way of listening for the document to be ready before executing code:

`$(document).ready(myApp.setup);`

We can change our current selectors and event listeners to use jQuery:

```
/* previous plain JS code
 * var helloButton = document.getElementById('helloButton');
 * helloButton.addEventListener('click', myApp.sayHello);
 */
 
 // jQuery code
 var $helloButton = $('#helloButton');
 $helloButton.on('click', myApp.sayHello);
 // helloButton.click(myApp.sayHello); // is another, equivalent jQuery function to add a click event listener

```

Not only is the jQuery code terser, it is also cross-browser (much more so, at least) compliant.


### jQuery Wizz-Bang Effects

Add a hidden message and style it.

```
  <style>
    .hidden {
      display: none;
    }

    .message {
      background-color: #aaf;
      border: 1px solid blue;
      border-radius: 5px;
      padding: 10px;
      line-height: 2;
    }
  </style>

  <p class="hidden message">you just clicked my button</p>
```

In the function that is called when the button is clicked, alter the code to no longer alert, but instead to use some jQuery animation.

```
  myApp.sayHello = function () { 
                 // alert('Hello WDI!'); 
                 var $message = $('.message').first();
                 $message.slideDown();
               }
```

Animations can be chained too:

```
  $message.slideDown().delay(1000).slideUp();
```

To be extra dilligent, we might also want to remove the class of 'hidden'

```
  $message.slideDown().removeClass('hidden');
```

or to ensure the next operation waits for the first to finish:

```
  $message.slideDown(1000, function () { $(this).removeClass('hidden') }
```

