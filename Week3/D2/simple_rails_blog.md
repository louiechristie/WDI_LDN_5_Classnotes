#A Simple rails Blog app:

Now let's create our first rails app:  
`$ rails new my_first_rails_app --database=postgresql`

Okay, let's now take a look at the folders our skeleton app has...  
`$ cd my_first_rails_app`
`$ subl .`

Look at all those files and folders! That's a skeleton rails app - it doesn't do anything yet. We will make sense of all this shortly, but first let's use some rails magic to turn this skeleton into a working rails app. 

We are going to use something called "rails generators" to write a lot of code for us, and give us an entire rails app in no time. The app we're going to build is a simple blogging app that will let users:

* CREATE blog posts with a title and body text
* CREATE comments on blog posts
* READ blog posts
  * see a list of all posts, with a summary
  * and read individual posts and their comments
* UPDATE blog posts
* DESTROY blog posts

We can create most of the code we need by running a few commands in the terminal:  
`$ bundle exec rails g scaffold post title:string text:text`  
`$ bundle exec rails g model comment post_id:integer text:text`  
`$ bundle exec rails g controller comments create`  

We just created a lot of ruby and html code that will let us perform CRUD operations on posts and comments. 

This isn't good enough to use in our final app, but it's a great help in getting started.

To create a database as well as posts and comments tables, we need to edit a file and run a couple more commands.

Edit `config\database.yml` by removing the pool, user and password fields.

Now we can run:  
`$ bundle exec rake db:create`

We just created a new database called "my_first_rails_app_development".  
`$ bundle exec rake db:migrate`  

As the message tells us, we just created new tables in that database. We can confirm this with `psql`.

Finally, we can now run our new rails app using the command:  
`$ rails s`

If we visit `localhost:3000`, we can see a standard rails welcome page. We'll get rid of that later (and will usually do this for each newly created rails app). For now visit the `/posts` path of the app.

Let’s create our first blog post.

The Rails Folder System:

A brand new rails 3 skeleton contains the following folders and files:

```
.
├── app
│   ├── assets
│   │   ├── images
│   │   │   └── rails.png
│   │   ├── javascripts
│   │   │   └── application.js
│   │   └── stylesheets
│   │       └── application.css
│   ├── controllers
│   │   └── application_controller.rb
│   ├── helpers
│   │   └── application_helper.rb
│   ├── mailers
│   ├── models
│   └── views
│       └── layouts
│           └── application.html.erb
├── config
│   ├── application.rb
│   ├── boot.rb
│   ├── database.yml
│   ├── environment.rb
│   ├── environments
│   │   ├── development.rb
│   │   ├── production.rb
│   │   └── test.rb
│   ├── initializers
│   │   ├── backtrace_silencers.rb
│   │   ├── inflections.rb
│   │   ├── mime_types.rb
│   │   ├── secret_token.rb
│   │   ├── session_store.rb
│   │   └── wrap_parameters.rb
│   ├── locales
│   │   └── en.yml
│   └── routes.rb
├── config.ru
├── db
│   └── seeds.rb
├── doc
│   └── README_FOR_APP
├── Gemfile
├── lib
│   ├── assets
│   └── tasks
├── log
├── public
│   ├── 404.html
│   ├── 422.html
│   ├── 500.html
│   ├── favicon.ico
│   ├── index.html
│   └── robots.txt
├── Rakefile
├── README.rdoc
├── script
│   └── rails
├── test
│   ├── fixtures
│   ├── functional
│   ├── integration
│   ├── performance
│   │   └── browsing_test.rb
│   ├── test_helper.rb
│   └── unit
├── tmp
│   └── cache
│       └── assets
└── vendor
    ├── assets
    │   ├── javascripts
    │   └── stylesheets
    └── plugins
```

⇒ 37 directories, 36 file

As we can see, Rails has a somewhat complicated folder structure - but it is there for a good reason. 

We've seen that for simple websites, the Sinatra approach of bundling most logic together in one file is fine. But we've also seen how we started to break our largest Sinatra apps down into folders and files. Rails just formalises the way we organize our code, enforcing a strict separation between different concerns. 


This is a good thing when working with anything other than the simplest of sites, for the following reasons:  

* large projects will always need breaking down into multiple files and folders in order to remain manageable
* as all rails sites have the same structure - it is easy to understand someone else's rails app, or our own when we come back to it months or years later
* because the folder structure is always the same, rails knows where to look for things, and so we can avoid using require_relative everywhere, rails takes care of 'requiring' our files for us
* 3rd party gems also know where to expect things to be. This has helped grow a massive ecosystem of gems that can enhance our rails app

This folder structure is just one of many CONVENTIONS that rails has. 

The design philosophy behind rails is "CONVENTION over CONFIGURATION". This means that as long as we want to use rails in a conventional way (and we usually will), we can avoid a lot of code-writing. 

For those occasions when we want to deviate from the norm, rails usually makes that very straight-forward too.

So many of the files that are generated when we create a new rails app won't need any changes made to them, and if they do, they're usually simple changes.
