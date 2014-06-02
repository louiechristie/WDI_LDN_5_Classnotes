# Routing

- So far, we've managed our app's state ourself. 
- We've added click handlers to things, and made our app state update accordingly. 
  - But this is kind of the boilerplate that we'd like our library to handle for us. 
  - And if we wanted to send someone a link to a specific album, we couldn't do it. 
- We can use Backbone's routers to handle this for us.

- So let's do that: let's add a static renderer first of all, just as a demo. 



  ```
  var AppRouter = Backbone.Router.extend({ 
    routes: {
      "": "index",
      "about": "about"
    }, 

    index: function() { 
      EventBus.trigger('display:gallery');
    },

    about: function() { 
      EventBus.trigger('display:about');
    }
  });

  var appRouter = new AppRouter();
  Backbone.history.start();
  ```
  
  
  - Defining a new router, that knows about two actions. A blank one, by default, and one to show an 'about' page. 
  - Starting the AppRouter, and telling Backbone to start routing things. 
  - Why history.start()? Because the docs say so. 

- We need to connect up a couple of other bits, though; get our AppView to respond to 'display:about'.


  ```
  showAbout: function() { 
    this.$el.html("<h3>All about my photo gallery</h3><p>This is a simple photo linkblog. Like pinterest, but mine.</p>");
    return this; 
  }
  ```
  

  - And in AppView.initialize: 
  
    ```
    _.bindAll(this, 'showAlbum', 'showGallery', 'showAbout');
    EventBus.on('display:about', this.showAbout);
    ```
    
- Now: add '#about' to our URL. About should show! 
  - Remove it! It'll disappear! Magic! We'll see the gallery view instead!
  - No page reload: it's a hash slug, so we're not reloading the page. But we could send someone a direct link to it...

- Neat! Let's do it with parameters. 
  - So we can view specific albums. 
  - Add to our routes: `"albums/:id": "showAlbum"`
  - Add a new method to our router: 
  
    ```
    showAlbum: function(id) { 
      // ...
    }
    ```
    
    - Augh! 
      - We're going to get a numeric ID into our `AppRouter::showAlbum()`, and we'd like to raise an event to say 'show this album'...
      - ... but our 'display:album' method is expecting an album as a payload, and our AppView doesn't know anything about the albums.
      - ... So let's refactor it so that our display:album event contains an integer as a payload instead. 
    - Refactoring: 
      - update our `EventBus.trigger` to transmit the ID, not the entire album. 
      - update our `showAlbum` method on `AppView` to be `showAlbum: function(albumId) {`
      - Add the line `var album = this.gallery.get(albumId)` as the first line. 
      - Update `MenuView::render()` to be `function render(albumId)`.
      - Refresh! 
        - Error... 'album' is not defined, from our MenuView. 
        - Add a `var album = null` in our `MenuView::render`. 
    - We have a refactoring quandary. 
      - Our original goal is to support navigation via routes. 
      - And that meant that our event should be broadcast with an ID, not an Album object. 
      
      - Our router emits events automatically for us with the name of the route. 
        - So if our AppView had access to our router, we could do `router.on('route:about', function() { /* ... */ });` instead of using our event bus. 
        - I don't know if you get parameters with this, but I expect so. Experiment! Read the docs!
      - Catch-all routes: wildcards. 
        - We could use this in our apps to make more general-purpose routes...
        - ... But we're going to use it to make a "404" page. 
          - Add to our routes: `"*stuff": "show404"`
          - Add a new method to our router: `show404: function(stuff) { EventBus.trigger('display:404', stuff); }`
          - Add a template:

            ```
            <script type="text/template" id="tmpl_404">
              <h2>404 Not Found</h2>
              <p>You tried to access <%= stuff %>, but our app doesn't know how to display it. Sorry.</p>
            </script>
            ```

          - Add a method to render it, and an EventBus.on, to our appView.
          - Even if we go to something with slashes in it, we get the whole path in.







 
 
 
 
 
 
 
 
 
 