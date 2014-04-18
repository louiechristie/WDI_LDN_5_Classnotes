#RAILS CONSOLE


###Bundler

<http://bundler.io/>

Bundler is designed to maintain differents environments based on the "gemfile". Bundler is also maintaining the dependencies of all the gems. Because we use "rbenv", each rails command have to be run within a bundle scope. 


For this we use:  
`$ bundle exec COMMAND`

Each time we run a rails or rake command, we should use bundle exec. To make this easier, let's create some aliases (in our ".zshrc" file):  
`alias be="bundle exec"`   
`alias rs="bundle exec rails s" `  
`alias rc="bundle exec rails c"`  


##Rake

<http://rake.rubyforge.org/>

Rake is a task manager for ruby.

Rake is useful for running tasks that change our application’s behavior or are part of the application ("personal rake tasks"). We can see all the rake tasks by typing `$ rake -T`

* `$ rake assets:precompile` : minify all assets files and unify them in one file
* `$ rake db:create` : run createdb
* `$ rake db:migrate` :
* `$ rake db:migrate` :down
* `$ rake notes` will display all notes beginning with [FIXME, OPTIMIZE, TODO]
* `$ rake routes`



##Rails generators

With "conventions over configuration" in mind, we understand why rails provide a lot of generators. These generators usually create files in the rails app tree. 

Typing `$ rails g `(same as `$ rails generate`) will list the generators we have at our disposal:

* `$ rails g controller`
* `$ rails g model`
* `$ rails g migration`
* `$ rails g scaffold`

`$ rails g model` can also take arguments for fields types, and will create a migration too.

The same goes for `$ rails g controller`:   
`$ rails g controller NAME [action action] [options]`

To illustrate this, let’s create another rails app. 

In terminal, run:  
`$ rails new blog-app `  
`$ cd blog-app`   
`$ rails g scaffold Post title:string content:text rake db:migrate`   

Let’s look at it in Chrome:  
`$ rails s `  

Now let’s expand it:  
`$ rails g scaffold Author first_name:string last_name:string dob:date picture:string 
`  
`$ rails g migration AddAuthorIdToPosts author_id:integer`


##Rails console

Not unlike "psql", "irb" or "pry", there is a console in rails. We access it by typing:  
`$ rails console `  
or   
`$ rails c`

And we can leave rails console by typing:   
`$ exit`  

Usually, we get into the console to manipulate models, check routes and debug the app.

We can use "pry-rails" to have the rails console with pry automatically interpreted. In order to do this, just add "pry-rails" to Gemfile in group development.

Let’s look at some of this functionality and play with models in our rails console:  
`$ author = Author.new `  
`$ author.firstname = "Julien"`   
`$ author.lastname = "Deslangles-Blanch"`

##Lazy Loading

By default, Rails uses "lazy loading". 

Lazy loading is a design pattern commonly used in computer programming to defer initialization of an object until the point at which it is needed. 

It can contribute to efficiency in the program's operation if properly and appropriately used. The opposite of "lazy loading" is "eager loading".

#ADDITIONAL RESOURCES

Rails console:  
<http://bundler.io/>  
<http://rake.rubyforge.org/>