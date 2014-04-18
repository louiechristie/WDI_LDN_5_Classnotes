#Rails MVC

Like Sinatra, Rails is built on the MVC pattern. In Sinatra we had effectively one "controller", while Rails lets us have many.

The Model View Controller principle divides the work of an application into three separate but closely cooperative subsystems.

![mvc diagram](https://lh6.googleusercontent.com/fvs2jVzy5F3aXDTY8xqa_orX5LncXu3soFyRvLDKtvBTrU-pr2dvkyqRB21n2vtzcf3MDYviI_uXv-xU8Am0c7nW1v7hOWABx0nMTZPD6VfHeEvrbIdmjob57g)


###Model (ActiveRecord ):

Maintains the relationship between Object and Database and handles validation, association, transactions, and more.

This subsystem is implemented in ActiveRecord library which provides an interface and binding between the tables in a relational database and the Ruby program code that manipulates database records. Ruby method names are automatically generated from the field names of database tables, and so on.

###View ( ActionView ):

A presentation of data in a particular format, triggered by a controller's decision to present the data. They are script based templating systems. This subsystem is implemented in ActionView library, which is an Embedded Ruby (ERb) based system for defining presentation templates for data presentation. 

Every Web connection to a Rails application results in the displaying of a view.

###Controller ( ActionController ):

The facility within the application that directs traffic, on the one hand querying the models for specific data, and on the other hand organizing that data (searching, sorting, massaging it) into a form that fits the needs of a given view.

This subsystem is implemented in ActionController which is a data broker sitting between ActiveRecord (the database interface) and ActionView (the presentation engine).

```


                                          -----> Model <----> DB
                                         |          |
            response         request     |          |
   Browser <-------- router -------> controller <--- 
                             GET            ^
                             PUT            |
                             POST           --> view <--> html/images/css/js
                             DELETE

```

##A tour of the rails folder system**

###config:

The config folder is where we configure our rails app e.g.:

* control what things are loaded when it starts up
* set various options that change the way rails runs
* lets us define different modes or "environments" that we can run rails in
  * production - a lean and fast mode that we use when we publish our finished site on the web
  * development - a mode that loads with lots of nice tools that make developing our sites easier (this is the default mode)
  * test - a mode that the site runs in when we are running automated tests on our site's code

Most of the files in here contain sensible defaults, and we rarely change them. There are two files that we usually need to change though:

* database.yml - this tells rails where our db is and how to connect to it
  * we've already changed this
  * note that this is environment specific
* routes.rb - this tells rails how to translate urls into method calls on our controllers
  * we've also changed this already to get our form working
  * note that this is very similar to the way routing is handled in sinatra

###gemfile:

This is a VERY important file that lets us:

* install all the gems our app needs with a single command
* and lets us carefully manage what versions we use
* and under what environments we load them e.g. we might have gems like pry that are only useful when developing our code  

Let's make "pry" the default rails console REPL by editing our Gemfile...

```
group :development do
  gem 'pry-rails'
end
```

Now we can run the commands:

`$ bundle install`  
`$ rails c`  

###app (home of MVC)

The 'app' folder is where most of the code for our application goes, i.e. the code we write. 

A quick scan of this file reveals that the rails folder system follows the MVC pattern. In other words, it expects us to split our code up into separate files that are responsible for different aspects of the web app.

###models (ActiveRecord and/or other ORMs)

The models folder is where we define the classes that make up our app. 

If we are making an astronomy app, we might have classes Star, Planet and Moon defined in files "star.rb", "planet.rb" and "moon.rb". 

As it is, we're making a blog app, and so (after running those generators) we have "post.rb" and "comment.rb" files, which contain definitions of the Post and Comment classes. 

If we were to later add the ability for users to create user accounts, then we would later add a "user.rb" file here that defined a User class. So, basically we always have a class for each of the key components that make up our app.

We usually want to store information about instances of these classes in a database. For instance, for our blogging app it makes sense to store instances of the Post and Comment classes in DB tables "posts" and "comments" respectively. That way we can have thousands of blog posts and their comments stored and queryable by SQL.

Our models generally want methods like `save`, `find`, update and `destroy` that are responsible for performing CRUD on the DB using SQL commands. 

Rather than write these ourselves, we usually use something called an ORM (Object Relational Mapper). 

By default, a new rails app loads a module called ActiveRecord that is an ORM that maps ruby objects to relational database tables. 

ActiveRecord contains a Base class which contains basic CRUD methods and more. 


We can add all these "superpowers" to our models by simply inheriting from this class, e.g.:

```
class Post < ActiveRecord::Base
end
```

If the "posts" DB table has columns "title" and "text", then in our application’s code, we can now type things like:

```
my_post = Post.new :title => 'My first blog post', :text => 'blah, blah, blah...'
my_post.save
```

...which would create a new instance of a Post in memory, before saving it to the posts table of our DB using a SQL INSERT command. 
We'll be taking a detailed look at this later. For now, it is good to just know that rails lets us work with ruby objects. It takes care of all the "ugly" SQL commands - making our lives way easier.

Models are also where we write methods that implement our app's logic. 
For instance, if our blog app needs to tell readers how many words a post has, we would add out `word_count` method to the Post class definition:

```
class Post < ActiveRecord::Base
  def word_count
    text.split.size
  end
end
```

We can play with our models in irb by typing $ rails console, or just $ rails c from our app's root folder. 

The rails console is essentially just a normal irb session, but one in which all our apps code has been loaded.


We can play with the methods inherited from ActiveRecord::Base here:  

```
Post
p = Post.new title: 'this post was made in the console', text: 'blah, blah, blah...'
p.word_count  #we can call our custom method on the new Post instance
p.id          #nil as not saved yet
p.created_at  #likewise
p.save        #INSERT record in DB, returning ID
p.id          #now we have an ID
p.created_at  #and a created_at (and modified_at) timestamp
Post.find 2   #we can find a post by it's id
Post.all      #and we can get an array of all posts
```

If we refresh our browser, we can see that we have just added a post to the DB. 

Notice how these models have methods corresponding to the columns in the DB table. 

This isn't anything to do with the `attr_accessible` method call, that's a security feature. 

It is not the same as `attr_accessor`. Rails has auto-magically added these methods to the Post class, because it has looked at the posts db table and discovered what columns there are.


###Associations:

Rails is clever, but it isn't able to guess the relationship between posts and comments, e.g.:  
`$ p.comments`

If we tell rails about the relationship we get sensible default behaviour. Add `has_many` :comments to Post, and `belongs_to :post` to Comment. Now we can reload our app code and try again:  
`$ reload!`  
`$ p.comments`  

We get an empty array. We can now add comments to a post:  
`$ c = Comment.new text: "this article is rubbish!"`  
`$ p.comments << c`  
`$ p.save`  

We've just associated a comment with a post.

Rails can now generate efficient SQL queries that rely on the relationship between models, e.g.:  
`$ p.comments`  
`$ p.comments.count`

So let's add a comment_count method to Posts:  

```
def comment_count
  comments.count
end
```

###views:

The "views" folder is similar to the "views" folder in Sinatra. 

By default, rails expects us to write our app's views using ERB (embedded ruby). 

Rails also expects us to organise our views a bit more, using one folder for each model. 
→ So if we have a Planet and Moon class, rails will expect us to have planets and moons folders within the views folder, and expects us to place all view templates related to those models in their respective folders. 

Rails lets us have more than one global layout for our app, and expects us to put them all in the layouts folder. By default it will use the layout called application.html.erb.
When rails substitutes the ruby in our views, it loads a module called ActionView that adds all kinds of useful things, including:

* lots of useful helpers for easily adding:
  * forms
  * image tags
  * etc.
* helpers that format text, numbers and dates:
  * e.g. turn a date into distance in words (instead of displaying a post time, we can easily print "posted about 3 hours ago")
* helpers that make it easy to create links to different parts of our app:
  * e.g. users_path to go to the page with all users
  * users_path(1) to go to the page that displays the user with with the id 1
As we can see, the rails scaffolding makes quite impractical views. 

For example, edit the last post, pasting in a long section of lorem ipsum into the text field. Let's fix the page that lists all our posts, and explore some of the these helpers that ActionView provides.

In index.html.erb:  
`<h1>Welcome to my blog</h1>`

```
<% @posts.each do |post| %>
  <h2><%= link_to post.title, post %></h2>
  <p>
    posted <%= time_ago_in_words post.created_at %> ago,
    word count: <%= number_to_human post.word_count, precision: 1 %>,
    comments: <%= number_to_human post.comment_count %>
  </p>
  <p><%= truncate post.text, :length => 300 %></p>
  <hr/>
<% end %>
```

`<p><%= link_to "New post", new_post_path %></p>`

Let's fix the show page for post while we're at it, so that it displays the post's comments as well:  

```
<h1><%= @post.title %></h1>
<%= @post.text %>
```

```
<h2>Comments</h2>
<% @post.comments.each do |comment| %>
  <p><%= comment.text %></p>
  <p><%= time_ago_in_words comment.created_at %> ago</p>
  <hr/>
<% end %>
```

```
<%= form_for [@post, @post.comments.build] do |f| %>
  <p><%= f.text_area :text, :size => '40x10' %></p>
  <p><%= f.submit "Post comment" %></p>
<% end %>
```

```
<p>
  <%= link_to "Back", posts_path %>
  |
  <%= link_to "Edit", edit_post_path(@post) %>
  |
  <%= link_to "Delete", @post, :method => :delete, :confirm => "Are you sure?" %>
</p>
```

###controllers:

In rails, the idea of a controller is to handle requests related to a given model (e.g. if our app has a Post class representing blog posts, then we would have a PostsController). 

This would be responsible for:  

* handling requests from the user related to blog posts
* calling methods on the Post model that satisfy that request i.e. methods that ultimately perform CRUD operations on the DB
* and then rendering the appropriate view

Controllers are therefore quite similar to the "main.rb" file we have in a sinatra app, with a few key differences:

* firstly, sinatra apps generally just have a single file containing all the logic - rails apps generally have multiple controller classes (and files), often one for each model class
* also, rails controllers don't map specific urls to different actions - that happens in a special file: `config/routes.rb`

Rails controllers inherit from ActionController::Base, which gives them some handy methods we'll explore later.

For now, just notice that the PostsController has a bunch of CRUD-related methods that each make method calls on the Post class, or instances of the post class, and then have some code that details what view to render. This is a little complicated because a rails app by default is set up to respond with more formats than just html, and has to render different views depending on whether CRUD operations are successful or not.

Our PostsController currently works fine, but our Comments controller needs a little work:

```
class CommentsController < ApplicationController
  def create
    @post = Post.find(params[:post_id])
    @comment = @post.comments.build(params[:comment])
    @comment.save

    redirect_to @post
  end
end
```

Let's focus on this simple example. 

This method will be called when a user submits a comment on a post. Notice that we have a params hash just like in Sinatra. We assume that this will contain a post_id key/value pair that tells us which post the comment should be added to. 

We can find that post in the DB and build a new Comment instance that includes this post's id, also passing in the params passed by the form. 

If we look at the form, we can see that it is just going to pass in a text field. We can now save the comment and redirect the user to the page that shows the post.

###assets:

This is where we store all our images, stylesheets and javascript files.


###helpers:

This is where we can define our own view helpers, to supplement those that ActionView provides.


###mailers:

This is where we can create templates for emails that our app needs to send to users.

###db:

This is where we store all files related specifically to the database. This can include:

* seeds.rb - a file that is responsible for populating the DB with initial data
* migrations - files that describe changes that we want to make to our app’s DB as we grow the app over time, e.g.:
  * adding new tables
  * adding extra columns to tables


###lib:

This is where we put any ruby code or assets that we want to share across models, i.e. this is where we would store any reusable modules we might write.


###public:

This acts just like our "public" folder in Sinatra. Anything in here can be linked directly in our views. 

In rails 2 and earlier, we used to put all our stylesheets and images in here, but now we just typically store error pages. This is because rails 3 has something called the "asset pipeline" that helps us manage assets much better. We'll learn about this in the coming weeks.