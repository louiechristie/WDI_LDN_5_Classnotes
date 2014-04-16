#Rails Folder Structure - Cheat Sheet:

* The majority of our project will be kept in the /app directory. 
* All of our CSS, images and javascript will be kept in our /app/assets directory.
* In our /controllers directory, we will keep all of our controllers, all of our controllers will inherit from our application controller. This is where we will keep all of our shared controller * methods.
* In our /helpers directory we will keep all of our modules which will be mixed in to our views. This will help keep logic out of our views and keep them tidy. 
* The /mailers directory will hold our email templates.
* All of our object models will be kept in our /models directory.
* The /views directory will keep all of our views, and partials. The /layout directory and application.html.erb will by default be in this directory
* The /config directory will store all the information for configuring our application. 
* The /locals directory will hold our information for internationalization. It will also store our database.yml. This will configure the connection to our database.
* Our application.rb in config/locals will be the starting file for all of our applications.
* The config/routes.rb file is where we will configure all of our roots.
* The /db directory will keep all our information regarding our database. It will also store all our database migrations, and our seed file.
* The /doc directory will store our readme file
* The /lib directory will store our bits of code that we would want to share over multiple apps.
* The /log directory will log everything which happens in the console.
* The /public directory will store all our public files. Static pages, images etc.
* The /script directory will keep all of our server scripts. 
* The /test directory is where we will keep all of our test files, including our specs directory for Rspec.
* The /tmp directory will keep our temporary cache files. 
* The /vendor directory will keep all of our code which is taken from other people. This is not often used anymore because we have gems.
* We will use our gitignore file to tell git what we do not want it to track.
* The config.ru will be used if you are deploying on a Rack-based server.
* The Gemfile will be where we add the gems we want to use.
* The Rakefile configures all of our rake tasks.


## Routing and Views

A few additional notes on routes and routing.

As seen previously, after creating our Rails app, we need to set our routes. We first need to remove our "public/index.html" file.

We now need to go and create some routes. First go to "home_controller.rb":

```
class HomeController < ApplicationController
 def about_us
  render text: "Hello from Rails. This is all about us!"
 end
end
```

Now open "config/route.rb".

Delete all the comments in this file and then write:  
`get '/about_us', to: 'home#about_us'`

This will create a "get" route which is at the "about_us" method in the home controller.

We are not able to go to `localhost:3000/about_us` and we will be displayed the text:

`Hello from Rails. This is all about us!`

Add this home method in the home controller:

```
def home
  render text: "Hello from Rails. This is the home root"
end
```

Then add another route to "route.rb":  
`get '/', to: 'home#home'`

This can also be written as:  
`root_to 'home#home'`  

Interestingly, with routes, we can also do calculations. To illustrate this, let’s change our home method to:

```
def home
  z = params[:x].to_i + params[:y].to_i
  render text: "Hello from Rails. This is the home root #{z}"
 end
```

Now if we visit `localhost:3000/1/2`

We will be returned:
`Hello from Rails. This is the home root 3`
Finally create the route and controller for home#faq

In order to use views properly, we will need to create a directory for each controller, and a template for each controller method.   
`$ mkdir app/views/home`  
`$ touch app/views/home/faq.html.erb`  

In our "faq.html.erb" type:  
`Hello this is the faq`

We can now create some links to go to our new pages. Go to "app/views/layouts/application.rb" and above the "yield" type:  

```
<nav>
  <ul>
    <li><%= link_to "Home", root_path %></li>
    <li><%= link_to "About", about_us_path %></li>
    <li><%= link_to "FAQ", faq_path %></li>
  </ul>
</nav>
```