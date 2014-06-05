# Backbone Events

## Our own custom events

- We don't have to stick to the DOM events. We can have our own.
- A little contrived as an example, but hopefully you'll be able to see the point. 
- Let's make our gallery log whenever something gets added to it. In our `Gallery` class: 



  ```
    var Gallery = Backbone.Collection.extend({ 
    model: Image, 

    initialize: function() {
        _.extend(this, Backbone.Events);
        this.on('added', function() { console.log("Added an image to this gallery."); });
    }
    });
  ```

  And, in our `GalleryView` `add:` 
  
  ```
  this.collection.trigger('added');
  ```
  
- Notice that we've added this on our Gallery itself, it's not going to propagate around our application. 
- We've told our collection to listen for an event.
- And we triggered it *outside that collection*, on the collection object. 



# Spreading events around our app

- A common backbone pattern is to use the Backbone.Events object itself as a 'dispatcher', a thing to share messages between components. 
- You might also call it an 'event bus'. 
  - You can 'broadcast' messages on this object, and anything that's interested can listen to them. 
  - This is known as 'publish/subscribe'. 
  - Another common pattern is to 'namespace' events with colons. 
    - No special meaning, just another character.
- Let's make our app listen for 'add' and 'remove' events on images, and make the gallery show and hide some helper text based on whether there are any images left. 


  - Add an EventBus object within our document.ready: `EventBus = _.clone(Backbone.Events);`
  - In `ImageView.remove`, trigger an '`image:removed`' event on the EventBus. 
  - In `GalleryView.add`, trigger an '`image:added`' event on the EventBus. 
  
  
  - We can add the following to help us follow our image:added / image:removed events. 
  
  ```
  EventBus.on('image:added', function() {console.debug('Saw an image:added event on the eventbus.')} );
  EventBus.on('image:removed', function() { console.debug('Saw an image:removed event on the eventbus')} );
  ```
 
### Flash messages
 
  ```
  <script type="text/template" id='tmpl_flash'>
    <%= message %>!
  </script>
  ```
  
  - Below the `Header` element add `<p id="flash"></p>`  
  - Create the view in application.js
  
  ```
  var FlashView = Backbone.View.extend({
    el: '#flash',

    render: function(message_arg) {
      var my_template = _.template($('#tmpl_flash').html());
      this.$el.html(my_template({ message: message_arg }));
      return this;
    }
  });
  ```
  
  - Create a new flashview `var flashview = new FlashView();`
  - Add an initialize function to the FlashView
  
  ```
  var FlashView = Backbone.View.extend({
    el: '#flash',

    initialize: function() {
      var that = this;
      EventBus.on('image:added', function() { that.render("Image added successfully ")});
      EventBus.on('image:removed', function() { that.render("Image removed successfully ")});
    },

    render: function(message_arg) {
      var my_template = _.template($('#tmpl_flash').html());
      this.$el.html(my_template({ message: message_arg }));
      return this;
    }
  });
  ```
  
 - To get our messages to fade, in the FlashView add
 
 ```
 showMessageThenFade: function(message){
      var that = this;
      that.render(message);
      setTimeout(function(){ that.$el.slideUp(); }, 2000);
    },
 ```
 
 - Update the initialize to call `showMessageThenFade` instead of `render`
 - In the render function add `this.$el.slideDown();` before `return this;`
 
### No images message

  - Add a `<p class="empty_gallery">No images in this gallery.</p>` within our `<ul id="demo_images">`. 
  - In our `GalleryView.initialize` method, add: 
  
    ```
    var that = this; 
    EventBus.on('image:added', function() { $('.empty_gallery', that.$el).hide(); });
    EventBus.on('image:removed', function() { if (0 === that.$el.children('li').length) { $('.empty_gallery', that.$el).show(); }});
    ```

 
# Refactoring

- We've made a bit of a mess here. Let's clean it up. 
- We've got two events, 'added' and 'image:added'. Let's move that to be just one. 
- We don't need our tmpl_hello any more; remove it. 
- We stuck a p into a list... that's a bit stinky. 
  - Our galleries are getting a bit complicated. Let's make our GalleryView manage an entire div, that happens to contain a list. 
  - Let's do that by adding a template for it. 
  
    ```
    <script type="text/template" id="tmpl_gallery">
      <div>
        <h2>Photo Gallery</h2>
        <p class="empty_gallery">No images in this gallery.</p>
        <ul class="gallery_images"></ul>
      </div>
    </script>
  ```
  
  - Add a <div id="gallery_container"></div> to our page.
  - Update our CSS (#demo_images → .gallery_images)
  - Update our JS. 
    - GalleryView `el: '#gallery_container'`
    - render() function on GalleryView: 
      ```
      render: function() { 
        var my_template = _.template($('#tmpl_gallery').html());
        this.$el.html(my_template());
        return this; 
      },
      ```
    - update appendItem(): `this.$el.append` → `$('ul', this.$el).append`
    - add `this.render();` to the bottom of the initialize method in the GalleryView     
    - update GalleryView initialize `if (0 === that.$el.children('li').length) {` → `if (0 === that.$el.children('li').length) {`





# Let's add gallery support.

- We've now got a system that lets us draw a gallery, and our images are already stored in a collection. 
- Wouldn't it be nice if we could have multiple galleries too? 
  - We'll need a new view that shows us the available galleries. 
- And there's a subtlety here...
  - We *could* try to just shove our Gallery Collection into another Collection...
  - ... But we probably want to have more than just a Collection of Collections. Each Gallery should probably have a title, for instance.
  - Or maybe we want an associated date-stamp, author, a description.
  - Let's rename things. We still have Images, and collect them into Albums. We can have many Albums in a Gallery. 
  - So, let's refactor our Gallery Collection into an Album Model, that *contains* a collection. 
  - And we'll have to update some of our other bits to suit. 


- Rename the Gallery to PictureContainer. 
- Create a new Album model:


  ```
  var Album = Backbone.Model.extend({
    defaults: {
        title: '',
        pictures: null
    },
    initialize: function() {
      this.set({pictures: new PictureContainer()});
    }
  });
  ```
  
- Update our GalleryView to work with our new Model, not a collection. 
  - Rename all occurrences of 'Gallery' to 'Album'.
    - And in lower case - CSS, HTML, selectors
  - Convert `this.collection = new PictureContainer()` to `this.model = new Album()` and update the binding accordingly. 
    - Collections raise an 'add' event automatically when something gets bound, but models don't, so we'll have to do that ourselves. `
  - Change `this.collection.add` to `this.model.add` in our AlbumView. 
  - Add an `add()` method to our Album: 
  
    ```
    add: function(image) { 
      var pictures = this.get('pictures');
      pictures.add(image);
      this.trigger('add', image);
    }
    ```

### Make form gallery specific.     

- Right now we've got one form for the whole app; let's make it album-specific. 
  - Remove form from main page and put it into the Album template.
  - Change form ID to form class. 
  - Move events hash from AppView to GalleryView. 
  - Move addImage from AppView to GalleryView, and rename to 'addImageFromForm'. Update in the events hash too. 
  - Update `this.gallery.add` to `this.add` in our `addImageFromForm` method.
  - remove `addImage` from AppView initialize `_.bindAll`
  
- Test it! This should work so far. 
  - But... removing an item shows our "no items" bit. 
  - Because .children() only traverses down one level. Change to .find().

- As we no longer need to setAlbum we can remove the initialize function from our appView
- & remove `appView.setAlbum(album);` from the bottom of application.js




### Multiple Galleries

- Let's build a view to show multiple galleries, and make our website show it by default.
  - Which means we'll need a new collection.
  - And a new template to go along with it. 

  - Add a Gallery collection and a view for it. 
  
    ```
    var Gallery = Backbone.Collection.extend({ model: Album });

    var GalleryView = Backbone.View.extend({ 
      tagname: 'div', 

      render: function() { 
        var my_template = _.template($('#tmpl_gallery').html());
        this.$el.html(my_template({albums: this.collection}));
        return this;
      }
    });
    ```
    
    - Nothing exciting here. We've seen this all before, right? 

  - Add a template: 
  
    ```
    <script type="text/template" id="tmpl_gallery">
      <h2>Available Albums</h2>
      <p>We have <%= albums.length %> <% if (1 == albums.length) { print("album"); } else { print("albums"); } %> in our gallery.</p>
      <ul>
        <% albums.each(function(album) { %>
          <li class="album" data-id="<%= album.cid %>"><img src="http://placehold.it/200x200" /><p><%= album.get('title') %></p></li>
        <% }) %>
      </ul>
    </script>
    ```
    - We're doing some more complex stuff in this template. 
      - We've got some logic, and are using Backbone's `print()` to produce some output. 
      - We're using .each on our collection to iterate over our objects. 
      - Each item is a Backbone Model, so we can call .get on it. 
      - Backbone models get a client ID property, called `cid`, that we can use to retrieve it from a collection later. 
      - This is kind of ugly, though. Beware! 

  - Rejig our AppView so it drops us on the Gallery list, and creates a gallery by default. 
    - Change our `initialize()` function to: 
    
      ```
      initialize: function() { 
        // Create a new gallery.
        this.gallery = new Gallery();
        var album = new Album({title: 'Bill Murray'});
        var image1 = new Image({title: 'Steve Zizzou', url: 'http://www.fillmurray.com/200/310'});
        var image2 = new Image({title: 'Venkman', url: 'http://www.fillmurray.com/205/320'});
        album.add(image1);
        album.add(image2);
        this.gallery.add(album);
        this.render();
      },
      ```
      
      - We set up some Backbone models, and then ask our App to display itself. 
      - And we're no longer calling `add()` on a View; we're calling it on a Model. The Model expects to get an Image model rather than two text fields. 
    - Add a `render()` method and a `showGallery()` function. 
    
      ```
      render: function() {
        this.showGallery();
        return this;
      },
      
      showGallery: function() {
        var galleryView = new GalleryView({collection: this.gallery});
        this.$el.html(galleryView.render().el);
        return this;
      }
      ```
      
      - We've created a `showGallery()` function that renders a new GalleryView, giving it the collection of Albums to show. 
      - We ask the galleryView we created to render itself, then got its content (remember, we did `return this` in it), and put that intou our view's HTML. 
      - Why not just do this in `render()`? That will soon become clear. 


### Adding album view for gallery
  - So now we can view all the galleries in our app... but not the albums themselves. 
  - Let's add a `showAlbum()` method that shows an album. 
    ```
    showAlbum: function(album) {
      var albumView = new AlbumView({model: album});
      this.$el.html(albumView.render().el);
      return this;
    }
    ```
  - And add something in our GalleryView that broadcasts a 'display this album please' message to the world. 
    - Update GalleryView to have a `tagName: 'div'` in it, and remove the container from our HTML file. 
    - Add `events: {'click li.album': "showAlbum" }` to GalleryView.
    - Add this to GalleryView: 
    
      ```
      showAlbum: function(ev) { 
        var clickedItem = $(ev.currentTarget);
        var id = clickedItem.data('id');
        EventBus.trigger('display:album', this.collection.get(id));
      }
      ```
      
    - Our GalleryView doesn't know how to display an album! 
      - But it can broadcast a message saying "Please display an album!" 
      - And it can include the album it wants to display. 
      - And we get the album to display from the data attribute of the HTML element. 

  - Now, our AppView should listen for the request to display an album, and display it. 
    - In AppView.initialize(): 
    
      ```
      _.bindAll(this, 'showAlbum'); // so "this" refers to the AppView, not the album
      EventBus.on('display:album', this.showAlbum);
      ```


    - In AlbumView update render to 
    
    ```
    render: function() {
      var my_template = _.template($('#tmpl_album').html());
      this.$el.html(my_template());
      var that = this
      _(that.model.get('images').models).each(that.appendItem, that);
      return this;
    },
    ```

- Phew! That was a lot of effort, wasn't it? 
- Let's see some benefit by adding more sample data. 


### Adding another album

  ```
  var album2 = new Album({title: 'kittens'});
  album2.add(new Image({title: 'Kitten!', url: 'http://placekitten.com/205/200'}));
  album2.add(new Image({title: 'Another kitten!', url: 'http://placekitten.com/205/320'}));
  this.gallery.add(album2);
  ```
  
  - And this is kind of neat; we've just created a couple of extra models, and our frontend has displayed it. And we can interact with these things. 
- Oh hey. Our 'no items' thing is still showing. 
  - Move the EventBus.on('image:added') code out of the initializer, and into the appendItem method (delete the EventBus handler).