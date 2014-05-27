# Backbone on Rails 

----
# Refresher
- What is Backbone? What's it for? 
  - It's a library/framework. It gives us the ability to structure our code and stops us having to write a bunch of boilerplate. 
  - And in the same way as we can have 'smarter' stuff in our Ruby rather than trying to do everything with arrays and hashes, it lets us have more complex objects.
- What objects does Backbone give us? 
  - Model, Collection, View, Router, Events.
- How were we doing templating? 
- Is Backbone more like Sinatra or Rails? 
  - I'd say Sinatra, because it's giving you some tools that take shortcuts rather than a whole bunch of infrastructure, generators, and strong conventions. 
- What's an event bus? 
- That photo gallery we were building. What's in it? 
  - Gallery, Album, Image.
  - How did they connect together? 

----

## Rails!

- So far, we've worked just in the browser. 
  - Which was kind of neat in itself! 
  - We've made an app that has a bunch of small components that co-operate. 
  - But they don't have close ties to each other, which helps us build complex apps. 
- Contrast this to the apps we've built in the past that just work on the server.
  - The server-only apps felt a bit static...
  - ... And our client-only app felt a bit like a toy.
    - Not to mention it's an accessibility nightmare. 
  - Let's get them to co-operate! 
  
 

- The app we're going to build today is JUST going to act as an API for our Backbone app.
  - Which means it's just going to return JSON. 
  - Is that progressive enhancement or graceful degradation? 
    - Neither! 
    - It's "Go fuck yourself if you don't have JavaScript!"
      - Fuck off, crap mobile browsers! 
      - Fuck off, people on flaky wifi! 
      - Fuck off, person who loaded the site when we had broken code live! 
      - Fuck off, people with screen readers! 
      - Fuck off, text browsers! 
      - Fuck off, search engines! 
      - Etc. 
    - But... sometimes this is OK.
      - Proof of concept
      - Lets us prototype quickly.
      - Add a web layer to a mobile app
      - Internal company apps.
- So, what models do we have in our Backbone app right now? 
  - Image, Album, Gallery
  - So if we wanted to save this stuff to the server, what would we want? 
    - Definitely Image and Album, and Album has_many Images. 
    - But... maybe skip Gallery for now? 
      - We only have one.
      - It depends what we're building! 
      - If we were going to have user accounts, then maybe we wouldn't skip it. 
        - We'd have a User model on the backend, and our User would has_one Gallery. 
      - But we're just building a demo project, so we'll skip it. 
      
### Build simple rails app

- So, let's build ourselves a simple Rails app. 
  - git status # Working directory clean
  - rails new galleryserver
  - git add galleryserver
  - git commit -m "New, empty Rails app."
  - cd galleryserver
  
  
#### Generate some models: 
- rails g model album title:string
- rails g model image title:string url:string album:references
- Add `has_many :images` to app/models/album.rb
- Let's add some seed data.
    
```
animals = Album.create(title: 'Cute Animals')
lols = Album.create(title: 'Lols')
wut = Album.create(title: 'Wut')
animals.images.create(title: 'Ferret', url: 'http://i.imgur.com/3e2khf9.jpg')
animals.images.create(title: 'Gerbil', url: 'http://i.imgur.com/a1zDQWb.gif')
animals.images.create(title: 'Raccoon', url: 'http://i.imgur.com/bEKHNj1.gif')
animals.images.create(title: 'Pokemon', url: 'http://i.imgur.com/9HBrTit.gif')
wut.images.create(title: 'Leeloo', url: 'http://i.imgur.com/zDJU8Be.jpg')
wut.images.create(title: 'Tongue', url: 'http://i.imgur.com/psIOBE3.gif')
wut.images.create(title: 'Ducks', url: 'http://i.imgur.com/swhxh17.jpg')
```
      
####Generate controllers: 



- rails g controller albums index show -e none
- rails g controller images show create --skip-template-engine
- Remove the routes created in config/routes.rb and add `resources :albums, :images, defaults: {format: :json}` instead
- The format: :json bit says we want to return JSON by default. 
- Let's start with the images. Edit app/controllers/imagecontroller.rb: 
    
```
class ImagesController < ApplicationController
 respond_to :json
 
 def show
   @image = Image.find params[:id]
   respond_with @image
 end
       
 def create
   @image = Image.new params[:image]
   if @image.save
     respond_with @image
   else
     respond_with @image, `status: :unprocessable_entity
   end
 end
end
```
      
- Class-level respond_to says we'll be responding to json, either via /images or /images.json.
- In show, we get an image, and return it to our requester. 
- In create, we try to create an image. If it succeeds, we return it. Otherwise we return it with an appropriate status. 
  - That should be everything in terms of our JSON serving for images. But we want to serve our app from our Rails server too. 
    - If we try to run it locally, then we will run into a thing called CORS, and it will really fuck up your day. 
      - Browsers have a thing called the same-origin policy, which stops them making requests to other domains. 
      - Sometimes that would be useful, like this time, when we have local files we want to talk to on the wider network. 
      - But otherwise, if code on one site could reach across to another, then if you went to a website while you were logged into Facebook...
      - ... it could do anything to your Facebook account it wanted. Create messages, read private messages, read your password when you typed it, steal your login cookies...
      - ... or do this to your bank...
      - This is why XSS - Cross Site Scripting - is a common class of security holes. It's allowing an attacker to run their code on your site, which would normally be prevented by the same-origin policy.
      - CORS is a way of relaxing these rules. A way of saying "Dude, it's cool, I'm an API. JavaScript can totally make requests to me."



- Anyway. Let's serve our content from our Rails app. 
  - rails g controller static index
  - add `root to: "static#index"` to our config/routes.rb
  - rm public/index.html
  - rm app/views/layouts/*
  - cp ../example/index.html app/views/static/index.html.erb
  - Replace all the OPENING <% TAGS ONLY WITH `<%%` to stop erb fucking them up
    - NOT THE CLOSING TAGS
  - Replace our stylesheet and CSS loads with calls to stylesheet_link_tag and javascript_include_tag
    - And copy those files into our app/assets
  - Visit http://localhost:3000 after starting the server... Should all work!


## The backbone bit
  - DO NOT FORGET THAT WE HAVE CHANGED FILES. WE COPIED OUR FILE TO app/assets/javascripts AND WE SHOULD EDIT THAT ONE NOW. 
  - ANOTHER LURKING FUCKUP: IF YOU LEAVE THE `#albums/3` BIT ON YOUR URL EVERYTHING WILL BREAK. 
  - Go back to Backbone. How do we do syncing in Backbone? 
    - It's pretty straightforward, actually. If we're dealing with a base case. 
      - Sadly, we're not. :(
      - Also, the documentation straight-up sucks. 
      - No good examples. 
      - Welcome to Stack Overflow. Population: you. 
    - If we were, we could just define some URLs on our Collections, and pretty much everything would be tickety-boo.
  - Let's start by getting our images from the server, instead of creating them by hand. 
    - Add a urlRoot attribute to our Image model: `urlRoot: '/images'`
      - Backbone understands REST
      - So it will try to do a '/images/1' to get the first image
      - And try to POST to /images to create new stuff. 
    - Update our AppView.initialize() to remove our hard-coded images, and get some specific ones from the server instead. 
    
      ```
      var image1 = new Image({id: 1});
      var image2 = new Image({id: 2});
      image1.fetch();
      image2.fetch();
      album.add([image1, image2]);
      ```
      
      - We're calling .fetch() to say "Hey, Backbone, go get the latest version from the server."
      - And we can add more than one in one go by passing an array to album.add. No biggie. 
    - Hit refresh... should work! 
  - What about saving our images? 
    - Where are we creating our images? (AlbumView::add())
    - Add an image.save() there.
    - Now if we create one from our form... it gets saved! 
      - But not into our album. Yet. We haven't taught our Backbone app OR our server about albums.

  - So we can retrieve individual images from our Rails app. What about our albums? 
  - We're going to need a controller that's pretty similar.
    - Edit app/controllers/albumcontroller.rb: 
    
      ```
      class AlbumsController < ApplicationController
        respond_to :json

        def index
          @albums = Album.all
          respond_with @albums
        end

        def show
          @album = Album.find params[:id]
          respond_with @album, :include => :images # Miss off the :include first, we'll explain it below. 
        end
      end
      ```
      
      - respond_to :json says "This controller is returning JSON only." 
      - respond_with in each action says what we want to return. 
      - We want to include our images as a nested attribute inside our album. Show it without the include first. 
      - Test with curl: 
        - curl http://localhost:3000/albums
        - curl http://localhost:3000/albums/1
        - You MIGHT be able to pipe these to json_reformat, if you're lucky. 
          - See also http://www.quilime.com/code/osx_print_json for OSX
        - Our app will also respond to /albums.json and /albums/1.json, if you don't like the idea of a web service that returns JSON by default.
          - Or if you wanted to render an HTML version. Hint, hint.
  - Our Rails app is done for now!
    - What does this mean? Git commit.
      - Don't need the assets for it, or the helpers. Fuck 'em. Delete 'em. 

#### Let's teach Backbone about our albums.
 
- Add a `urlRoot: '/albums'` to our `Album` model.
- Add a `url: '/albums'` to our `Gallery` collection.
  - Our Gallery is a collection of albums, remember? 
  - Update our AppView.initialize to be `this.gallery = new Gallery(); this.gallery.fetch()`
- Hit refresh! It's broken! 
  - Why is it broken?
  - Because are rendering before we get any data back from the server. Asynchronous! 
  - Update our fetch to be `this.gallery.fetch({success: this.render })`
    - We added a callback, just like in a normal AJAX request. 
    - Make sure render is included in our _.bindAll.
- But... it's still kind of broken... click an album. They're all empty. 
  - Backbone collections can auto-populate their models for us. But an Album isn't a Collection, it's a Model. 
  - So we can't just hit an endpoint that pulls down stuff and it'll auto-populate. 
  - We *could* try to handle this manually...
    - We could set a flag on our album model that tells whether it's tried to get the images for the album yet.
    - And then retrieve them ourselves if we haven't. 
      - Remember we built in those details with our album call? 
      - We get a `.parse()` method in our Backbone models that we can override that controls how our JSON gets converted to models. 
  - But let's let Backbone do most of the work for us, this time. 
- Our Album model contains an ImageContainer collection. Let's teach it to retrieve our images for us. 
  - Via the #show method on the Album model. Which means it needs to know the ID of the album. 
  - Thus, update the initialize method of our Album model to: 
            
        ```
        initialize: function() {
          var container = new ImageContainer();
          container.id = this.id;
          container.fetch();
          this.set({images: container});
        },        
        ```
        
      - Our Albums are always being initialized by gallery.fetch(), so they'll always have an ID by this point. 
      - And tell our ImageContainer where its endpoint is on the server: 
      
        ```
        url: function() { 
          return "/albums/" + this.id;
        }
        ```
        
      - Let's review what we get back from our server: `curl http://localhost:3000/albums/1`
      - Our Collection is expecting to get back a JSON array containing Image objects. Is that what we get? 
        - No! 
        - We get the Album data too. 
      - Add a `parse` method to ImageContainer: 
      
        ```
        parse: function(data) { 
          return data.images;
        }
        ```
        
      - That's it! 
      - Our albums now construct correctly. 

### Image creation and deletion 
  - What about image creation and deletion? 
  - Let's try to create an image. What happens? Does it work?
    - It shows up in the page...
    - ... and our rails console shows it saved it. 
    - So did it work? NO! No album_id. 
  - Backbone doesn't do relationships for us automatically. 
  - How are we going to fix this? 
    - We need to set our album_id. 
    - add album_id to our attr_accessible line in app/models/image.rb
    - Where in Backbone? Where are we creating our Image models? (This is a good question to ask the class.)
      - It's in our AlbumView, an add method. 
      - We could just add an `image.set('album_id', this.model.id)` to our AlbumView add method...
      - (Go ahead and add it, prove that it works)
      - But we're being a bit stinky. A View instantiating a new Model? Really? 
      - Let's move the add method out of AlbumView and into Album.
        - Move the method from AlbumView to Album, remove it from _.bindAll in the AlbumView initializer.
        - Rename it to 'addNew'
        - update calls to 'this.model' to 'this'
        - update call in AlbumView::addImageFromForm from `this.add($titleInput.val(), $urlInput.val())` to `this.model.addNew($titleInput.val(), $urlInput.val())`. 
      - We're no longer using our Album::add, but let's update it so we can work with it anyway. 
      - Add `image.set('album_id', this.id); image.save()` to our Album::add method. 
        - Backbone calls `model.isNew()` to determine whether to hit 'create' or 'update'.
    - Check it still works, git commit. 
  - What about deletion? 
   
   - What happens if we click the X button?
   - The Image's destroy() method gets called, which will automatically try to delete it on the server. 
   - We haven't implemented delete yet. Let's do that. 
   - In app/controllers/image_controller.rb: 
     
       ```
       def destroy
         @image = Image.find params[:id]
         if @image.destroy
           respond_with :status => :no_content
         else
           respond_with @image, :status => :internal_server_error
         end
       end
       ```
        
      - :no_content maps to an HTTP 204, "No Content". 2xx, so success. It's a delete, so nothing to send back. 
      - That should now work!