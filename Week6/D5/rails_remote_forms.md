# Unobtrusive Javascript

  git@github.com:Pavling/wdi-w6d5-rails_ujs.git

We're going to look at how Rails makes use of unobtrusive JavaScript.

Unobtrusive JavaScript is a technique for separating the behaviour of a web application from its content in a similar way that CSS allows us to separate presentation from content.

Before we start using unobtrusive JavaScript in Rails we'll show you an example in a simple HTML document.

rails_ujs/public/hello_world.html


In the page we have a link with an onclick attribute that contains some JavaScript code. As the script is contained inline within the HTML it is not unobtrusive and this is a bad thing because we're mixing content and behaviour.

The same applies with JavaScript. Putting small pieces of JavaScript in the attributes of the HTML elements on a page mixes concerns and makes websites difficult to update. Moving JavaScript out into separate files reduces duplication, makes refactoring possible and makes it much easier to write and debug complex web applications.

How do we make the JavaScript in our simple example unobtrusive? The main step is to move the JavaScript in the onclick attribute into a separate file and to use a JavaScript framework, in this case jQuery, to wire up the scripts to events on elements. We'll show you the modified page and then explain the changes we've made.


rails_ujs/public/hello_world-unobtrusive.html


Okay, we've not moved the JavaScript out into a separate file, but this is only to make it more easy to show the changes.

In the link element in the body we've removed the onclick attribute and replaced it with an id so that we can target the link from the jQuery code. Then, in the head section we've added a reference to the jQuery library and, below that, the script that should be in the external file. The script starts by calling jQuery's $ function with a function as an argument. The function in the argument will be called when the page's DOM loads and the code in it contains one line of jQuery code that finds the link by its id and attaches a function to its click event. That function contains the alert and then returns false so that the link isn't followed.

If we reload the page now it will behave exactly as it did before with the alert being shown when the link is clicked.

It might seem that we've had to do a lot of work here for not much effect but the simple example we've used doesn't really show the advantage of unobtrusive JavaScript as we've taken one line of inline JavaScript and turned it into six. This example isn't meant to prove the advantage of unobtrusive JavaScript but just to show how it's done and how to compare it.

The benefits of unobtrusive JavaScript won't really appear until our web application has a lot more JavaScript in it as then the advantages of having all of the scripts in a separate file and being able to remove duplication begin to show themselves.

One problem with this approach is that the JavaScript is usually located in a static JavaScript file. How do we insert dynamic server-side content into the JavaScript now that we can't do it inline?

In HTML 5 we can use custom data attributes to store data related to an element on a page. These are the same as any other attributes but the name must begin with data-. To store the message that is shown when the link is clicked in a data attribute we can rewrite the link thus:


rails_ujs/public/hello_world-extended.html


If we reload the page now we'll see the message from the data attribute.


## How Rails 3 Uses Data Attributes

Rails 3 uses these custom data attributes in its approach to unobtrusive JavaScript as a way of passing data to JavaScript. We'll take a look now at how this applies to a Rails 3 application. Our application is a basic e-commerce app that has a list of products that can be searched against. There are also links to edit and destroy products and it's when we try to destroy a product that we find a problem as the link seems not to work.

The code in the view that generates the “Destroy” link is a standard link_to method with a ":data {:confirm}" option to display a JavaScript confirm alert and a :method option that is set to :delete so that the request is submitted as a DELETE request rather than as a GET

Look at the HTML source that this code generates:


`<a href="/products/8" data-confirm="Are you sure?" data-method="delete" rel="nofollow">Destroy</a>`


In Rails 2 using link_to to create a destroy link generates a lot of inline JavaScript to create the confirm dialogue box and a form that would simulate a DELETE or PUT request. By comparison the Rails 3 code is much neater and makes use of the HTML 5 data attributes we showed earlier, creating one called data-confirm that holds the confirmation message and another called data-method that holds the method.

This works by using functionality built in by two lines in our application's layout file:


/app/views/layouts/application.html.erb
`<%= javascript_include_tag :defaults %>`  
`<%= csrf_meta_tag %>`  


The first line includes the standard JavaScript files for a Rails application. The second line creates two meta tags that hold the authenticity token that is necessary to make DELETE requests.

Without the JS functionality, the "delete" link will send a GET to the router for the '/products/8' route... and will show the product.


## Adding AJAX to The Search Form

Next we'll modify the search form on the index page so that it makes use of AJAX when it's submitted rather than making a GET request. The code for the index view that contains the form is shown below:


In earlier versions of Rails to get the form working via AJAX we would have replaced the form_tag with form_remote_tag. This method generates a lot of inline JavaScript, however, which is exactly what we're trying to avoid.

Instead, we'll just add a "remote: true" parameter:

  `<%= form_tag products_path, :method => :get, :remote => true do %>`

This :remote parameter can also be used with other helper methods such as link_to, button_to and form_for. If we reload the page and look at the source we can see how the new form code works.

```
<form action="/products" data-remote="true" method="get">  <p>
    <input id="search" name="search" type="text" />
    <input type="submit" value="Search" />
  </p>
</form>
```

The form element is the same as it was before the remote parameter was added but has a new data-remote attribute. There's no inline JavaScript, the new attribute is enough to tell the JavaScript that the form should be submitted via AJAX.

But if we submit the form, nothing happens... that we see.

We need to write code to handle the response from the AJAX call. The list of products is contained in a div with an id of products so we can update the contents of this div to display the relevant products. The form is submitted to the ProductController's index action and we need to add a handler for JS, and a new view template to handle JavaScript requests called index.js.erb.


  # products_controller.rb
  format.js

  # products/index.js.erb
  $("#products").html("<%= escape_javascript(render(@products))%>");


We can write any JavaScript we like in this template file and it will be executed when it is returned to the browser. The code in the new template will update the contents of the products div with the list of products.


If a user views our application using a browser that doesn't have JavaScript enabled the form will degrade gracefully and make a normal GET request when it is submitted. Destroying products will not work, however. This is a common problems caused by the fact that HTML links can only make GET requests


to get rid of the need to submit our form:

```
$(function() {
  $('#search').on('keyup', function() {
    $('#search_form').submit();
  })
} );
```


#The Cat Walk

Who needs Milan when you have JavaScript?

Start with this webpage, which has a single img tag of an animated GIF of a cat walking.

  <!DOCTYPE html>
  <html>
   <head>
    <meta charset="utf-8" />
    <title>Cat Walk</title>
   </head>
   <body>

    <img style="position:absolute;" src="http://www.anniemation.com/clip_art/images/cat-walk.gif">

   </body>
  </html>

- Add a script tag at the bottom of the page, where you'll put all your code.
- Create a variable to store a reference to the img.
- Change the style of the img to have a "left" of "0px", so that it starts at the left hand of the screens.
- Create a function called catWalk() that moves the cat 10 pixels to the right of where it started, by changing the "left" style property.
- Call that function every 50 milliseconds. Your cat should now be moving across the screen from left to right. Hurrah!



#Solution

```
var img = document.getElementsByTagName('img')[0];
img.style.left = '0px';
function catWalk() {
  img.style.left = (parseInt(img.style.left) + 10) + 'px';
}
window.setInterval(catWalk, 50);
```

Bonus #1: When the cat reaches the right-hand of the screen, restart them at the left hand side ("0px"). So they should keep walking from left to right across the screen, forever and ever.

#Solution
```
var img = document.getElementsByTagName('img')[0];
img.style.left = '0px';
function catWalk() {
  var currentLeft = parseInt(img.style.left);
  img.style.left = (currentLeft + 10) + 'px';
  if (currentLeft > (window.innerWidth-img.width)) {
    img.style.left = '0px';
  }
}
window.setInterval(catWalk, 50);
```

Bonus #2: When the cat reaches the right-hand of the screen, make them move backwards instead. They should keep walking back and forth forever and ever.

#Solution

```
var img = document.getElementsByTagName('img')[0];
img.style.left = '0px';
var walkForwards = true;
function catWalk() {
  var currentLeft = parseInt(img.style.left);

  if (walkForwards && (currentLeft > (window.innerWidth-img.width))) {
    walkForwards = false;
  }
  if (!walkForwards && (currentLeft <= 0)) {
    walkForwards = true;
  }

  if (walkForwards) {
    img.style.left = (currentLeft + 10) + 'px';
  } else {
    img.style.left = (currentLeft - 10) + 'px';
  }
}
window.setInterval(catWalk, 50);
```

Bonus #3: When the cat reaches the middle of the screen, replace the img with an image of a cat dancing, keep it dancing for 10 seconds, and then replace the img with the original image and have it continue the walk.













