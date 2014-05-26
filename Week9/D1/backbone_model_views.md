# Backbone

- Frameworks help us structure our code, and keep it neat. 
	- And stop us having to write boilerplate (routing, rendering, DB access)
  	- And help us work with others by following the conventions that spring up around particular frameworks. 
- They're particularly beneficial when working with larger applications.

## What is it?

- Backbone is a JavaScript framework. 
- Just like Rails makes it easier for us to write Ruby web apps, Backbone makes it easier for us to write JavaScript apps. 
- Much like Rails, it's built using model-view-controller. 
- We use Backbone to build bigger apps; it gives us some support, and does some 'heavy lifting' for us. 

# Getting started

- Create a directory in your classwork folder for this project. 
- Go download Backbone: <http://backbonejs.org/> and get the development version. 
  - Depends on Underscore: <http://underscorejs.org/>
  - And on jQuery: <http://jquery.com/download/>
- Let's get it up and running. 
  - create an index.html
  - include the dependencies
  - include backbone
  - Check that Jquery, underscore and backbone are included properly. 
  - In Dev tools / console `$`, `underscore`, `Backbone` 
  
  - Create a div with an id of `app_container` and a string saying the application is loading.
  - Create a JS file to contain your app, and include it. 
  - Git commit. 


##### index.html

```
<html>
  <head>
    <title>Backbone Lesson 1</title>
    <script src="js/jquery-1.11.0.js"></script>
    <script src="js/underscore.js"></script>
    <script src="js/backbone.js"></script>
    <script src="js/application.js"></script>
  </head>
  <body>
    <h1>My first backbone application</h1>

    <div id="app_container">
      <p>Please wait, application loading...</p>
    </div>
  </body>
</html>
```

Test that our application.js loads
##### application.js
`alert("Yay")`


## Backbone Views

- In this case we're creating a static HTML page, but this *could* be a view rendered by our web server.
- Backbone has a concept of 'views' that are slightly different. 'View' doesn't mean "an HTML page rendered by the server", it means "a view onto some data". This is true when talking about the backend too. 
- A Backbone view has an HTML element - a DOM element - that the JavaScript view 'looks after'. It's going to handle displaying it and updating it for us. 
- Let's start creating a view for our main app. 
  
  ```
	$(document).ready(function() {
	  var AppView = Backbone.View.extend ({
	    el: '#app_container',
	
	
	    initialize: function() {
	      alert("I am the initializer");
	    }
	  });
	});			
  ```
  
  
  ```
	$(document).ready(function() {
	 var AppView = Backbone.View.extend ({
	   el: '#app_container',
	
	   initialize: function() {
	     alert("I am the initializer");
	   }
	 }); // End AppView
	
	 var aardvark = new AppView();
	}); // End document.ready
  ```

- Refresh your page. It should show you an alert.
- Let's do a render method.

  ```
  render: function() { 
    this.$el.html("Hello world!"); 
    return this; 
  }
  ```

- Every Backbone View gets a utility variable $el that we can use. It's a jQuery object. 
- We must `return this;` so that we can chain functions together.


Update our initialize function to

```
initialize: function() {
   this.render();
 },
```

### Templates

- It's a bit crap if we have to put our HTML in our JS file. 
  - Bad separation of concerns
  - Makes our JS big
  - Harder to change
- Let's put it in a template instead. 

In the `<head>` element of our index.html add the following below our script tags

  ``` 
  <script type="text/template" id="tmpl_hello">
    <p>Hello, <%= name %></p>
  </script>
  ```

- And update our render method to use it. 

  ``` 
  render: function() { 
    var my_template = _.template($('#tmpl_hello').html());
    this.$el.html(my_template({name: 'Alex'}));
    return this; 
  }
  ```

- Hit refresh, see it. 
- Do you see how this works? 
  - We've grabbed the contents of our template, and instantiated an Underscore template with it, which we called `my_template` . 
  - We then updated the HTML of our element by calling our template, and passing it some data. 
  

## But this is still static data...

- But we're still rendering static stuff. Let's make it more dynamic. 
  
```
  getName: function() { 
    var names = ["Alex", "Tim", "Clem", "Andy"];
    return names[Math.floor(Math.random() * names.length)];
  }

  render: function() { 
    var my_template = _.template($('#tmpl_hello').html());
    this.$el.html(my_template({name: this.getName()}));
    return this; 
  }
```

Add a button to our index.html template

```
<!-- Document templates -->
<script type="text/template" id="tmpl_hello">
  <p> Application loaded. Hi, <%= name %>!</p>
  <button id="redraw">Re-render the page and great someone else </button>
</script>
```

then add the `events: {'click button#redraw': 'render'},`

```
var AppView = Backbone.View.extend ({
    el: '#app_container',
    events: {'click button#redraw': 'render'}, 
```


## Models

- This is supposed to be a photo gallery, and we've got a basic proof of concept up that shows we can update our HTML from Backbone. 
- Let's add an image. 
  - We're going to create an Image model, and an associated view for it. That is, a way to render one image. 
  - We're also going to create a Collection - something to hold our images. 
  - Then we're going to create a view for our collection. 
  - Our collection will then draw a new image whenever we add an image. 

- Create the model, and its view. 


```
  Image = Backbone.Model.extend({
    defaults: {
      title: '',
      url: ''
    }
  });

  ImageView = Backbone.View.extend({
    tagName: 'li',

    render: function() {
      var my_template = _.template($('#tmpl_image').html());
      this.$el.html(my_template({title: this.model.get('title'), url: this.model.get('url')}));
      return this;
    }
  });
```

  - (Note we're passing in the params from our model, and we use .get() to retrieve the values.)
  - We use tagName instead of an el, because we want to create a new one each time. See docs. 

<http://backbonejs.org/>

```
<script type="text/template" id="tmpl_image">
  <h1><%= title %></h1>
  <img src="<%= url %>" alt="<%= title %>" />
</script>
``` 

We are going to need to create a ul for our images to render into

```
 <body>
    <h1>My Photo Gallery</h1>

    <ul id="demo_images">

    </ul>

    <div id="app_container">
      <p>Please wait, application loading...</p>
    </div>
  </body>
```

	``` 
	 var Gallery = Backbone.Collection.extend({
	     model: Image
	 });
	
	 var GalleryView = Backbone.View.extend({
	     el: '#demo_images',
	
	     initialize: function() {
	         this.collection = new Gallery();
	         this.collection.bind('add', this.appendItem, this);
	     },
	
	     add: function(title, url) {
	         var image = new Image();
	         image.set('title', title);
	         image.set('url', url);
	         this.collection.add(image);
	     },
	
	     appendItem: function(image) {
	         var imageView = new ImageView({ model: image });
	         this.$el.append(imageView.render().el);
	     }
	 });
	
	 var gallery = new GalleryView();
	 gallery.add('Steve Zizzou', 'http://www.fillmurray.com/200/310');
	 gallery.add('Venkman', 'http://www.fillmurray.com/205/320');
  
	```

  - Our initialize function creates a new Gallery. 
  - Our add method creates a new Image, and adds it to our gallery. 
  - Our appendItem method gets called automatically whenever we add an item to the gallery. This method draws it. 
  - we bind our 'this' to the View object, so when this.appendItem is called it gets the correct context. 





### CSS break! 

- Let's make this less ugly. 

- background color
- header
- margins
- images all on one line. 

```
	body {
	    background-color: #aac;
	}
	
	#container {
	    max-width: 960px;
	    margin-left: auto;
	    margin-right: auto;
	}
	header {
	    text-align: center;
	    background-color: #004;
	    color: #eee;
	    min-height: 80px;
	    border-bottom: 1px solid #eee;
	}
	
	header h1 {
	    margin-top: 0;
	}
	
	li {
	    display: inline-block;
	    border: 1px solid black;
	    margin-right: 5px;
	    vertical-align: top;
	}
	li h1 {
	    font-size: 120%;
	    text-align: center;
	}
```

## Your turn: an address book 

- So it's time for a lab. Let's start building a basic address book. 
  - Create a model to represent a person.
  - And a template to display it. 
  - And a view that handles the displaying of the content. 
  - And hook it up together. 
  - Don't worry about entering data for now, just hard-code something for the time being. 
  - And you can probably not worry too much about the nested views for now, just have the one controller. 



## Adding our own images

So... we can now add images in our code. Let's allow the user to add images themselves. 

- Let's remove the 'app_container' div. That was just for prototyping now. 
- Update the el for the appcontroller to match the new ID. 
- Add a form to the HTML. 

```
<form id="add" action="#">
    <label for="title">Title</label><input id="title" name="title" placeholder="Your image's title" />
    <label for="url">URL</label><input id="url" name="url" placeholder="http://www.example.com/picture.jpg" />
    <input type="submit" value="Add" />
</form>
```

- Add an events property to our AppView. 
```
events: {'submit form#add': 'addImage'},
```

- Add an 'addImage' method to the AppView.
```
addImage: function(ev) {
    ev.preventDefault();
    var $titleInput = $('#title');
    var $urlInput = $('#url');
    this.gallery.add($titleInput.val(), $urlInput.val());
    $titleInput.val('');
    $urlInput.val('');
}
```
  - ev.preventDefault() to stop the form actually being submitted
  - Pass the details through to the gallery
  - Clear the input fields, ready for the next addition.


```
$(document).ready(function() {

  var AppView = Backbone.View.extend ({
    el: '#app_container',
    events: {'submit form#add': 'addImage'}, 

    initialize: function() {
      _.bindAll(this, 'setGallery', 'addImage');
      this.render();
    },

    addImage: function(ev) {
      ev.preventDefault();
      var $titleInput = $('#title');
      var $urlInput = $('#url');
      this.gallery.add($titleInput.val(), $urlInput.val());
    },

    setGallery: function(galleryView){
      this.gallery = galleryView;
    }

  }); // End AppView

  var Image = Backbone.Model.extend({
    defaults: {
      title: '',
      url: ''
    }
  });

  var ImageView = Backbone.View.extend({
    tagName: 'li', 

    render: function() {
      var my_template = _.template($('#tmpl_image').html());
      this.$el.html(my_template({ title: this.model.get('title'), url: this.model.get('url') }));
      return this;
    }
  });

  var Gallery = Backbone.Collection.extend({ model: Image });
  var GalleryView = Backbone.View.extend({
    el: '#demo_images',

    initialize: function() {
      this.collection = new Gallery();
      this.collection.bind('add', this.appendItem, this);
    },

    add: function(title, url) {
      var image = new Image();
      image.set('title', title);
      image.set('url', url);
      this.collection.add(image);
    },

    appendItem: function(image) {
      var imageView = new ImageView({ model: image });
      this.$el.append(imageView.render().el);
    }
  });

  
  var gallery = new GalleryView();
  var appView = new AppView();
  appView.setGallery(gallery);

  gallery.add('Steve Zizzou', 'http://www.fillmurray.com/200/310');
  gallery.add('Venkman', 'http://www.fillmurray.com/205/320');
  gallery.add('Phil Connors', 'http://www.fillmurray.com/200/300')

}); // End document.ready
```

# What about delete? 

- We can add this just by reference to the Image; our Gallery doesn't care. 
- Add some JS to remove it: 

Add a span tag to our image template

	```
	<script type="text/template" id="tmpl_image">
	  <span class="closebutton">X</span>
	  <h1 ><%= title %> </h1>
	  <img src="<%= url %>" alt="<%= title %>">
	</script>
	```

  ```
  // In ItemView.
  events: { 'click .remove', 'remove' },
  remove: function() { 
    this.$el.remove();
  }
  ```
  - Remove, not delete, because delete is a reserved word
  - .remove() is a jQuery method. 
- Now update our template. 
- Done! 
- Style it. 

- A warning! 
  - We've just removed it from the *view*... not the collection. 
  - We'll fix this later. 

# Side Note - Sublime Text 2 recognize underscore templates as HTML
 
1. Go to "Browse Packages" in the menu (where the menu item is depends on your platform).
2. Open up HTML/HTML.tmLanguage
3. Change this line (line 286 in my HTML.tmLanguage):

`<string>(?:^\s+)?(&lt;)((?i:script))\b(?![^&gt;]*/&gt;)</string>`

to this:

`<string>(?:^\s+)?(&lt;)((?i:script))\b(?![^&gt;]*/&gt;)(?!.*type=["']text/template['"])</string>`

Now any script tags with type="text/template" or type='text/template' will render as html and not javascript.















