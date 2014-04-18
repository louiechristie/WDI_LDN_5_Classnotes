# Rolling on Rails

###What is Rails

"Rails" is the web app framework we've been building up to over the past couple of weeks. By ways of comparison, Rails is like Sinatra’s big brother - only much more capable. 

Rails is:

* a web development framework written in Ruby
* that separate views (html & css) from logic
* and is intended to make developing modern websites straight forward

Rails came first, and was invented by David Heinemeier Hansson (DHH) of 37 Signals. 

He started out writing Basecamp, a project management tool, and later realised that most of the code was general purpose enough that it could be used to build any modern website. Thus rails was born. It is now collaboratively developed by thousands of developers on GitHub. 

Rails has been largely responsible for the popularity of the Ruby programming language which first appeared in 1995, thanks to Yukihiro Matsumoto a.k.a "Matz".

Sinatra was made by Blake Mizerany a few years later in 2007. Sinatra was a reaction to Rails. Many Ruby fans loved the idea of a ruby web framework, but believed that Rails was over-bloated for relatively simple websites in terms of:

* amount of computer resources needed to run a rails-powered website
* amount of time you (the developer) need to spend learning the framework

The first problem is no longer true as rails is very modular and so it is easy to use fewer resources by changing the defaults. A rails site can be as lightweight as a Sinatra site now, and they're actually both built on the same stack (RACK). 

The main difference now is that while Sinatra, by default, only includes essential modules, rails includes a lot of 'nice to haves'. 

You can easily extend Sinatra to include many of these default tools, and likewise, you can easily tell rails not to include them, and so choosing between the two is really a case of deciding whether it is quicker to say what tools you DON'T need in your app (i.e. use rails if you need lots of advanced tools) or what tools you DO need (i.e. use sinatra if you only need the basics)

The criticism of rails that it is too complex to use is still true to an extent - it is considerably more complex than Sinatra and other small frameworks. Learning how code is organised in rails is going to take a bit of work, but as we will see, it is well worth the effort!

![Rails](https://lh6.googleusercontent.com/02MiBFjo_tF0DLly-q3JbmMKf2Kn23N0Kux6-o2hrHwqoUfzYlaogau-LEJ1-P2Q2-gXBey6u3SfYT0PEuzPAlwOZ-KMa-pFcxiWdkMCz3k7aoEfK0IvcSIIuw)


## Installing Rails

run the following commands in the terminal:

`$ gem install rails -v=3.2.17`  
`$ rbenv rehash`  

This has just installed the rails gem (and several gems that it depends on), and the rehash has made sure that the rails command is now accessible.

Let's use that command to make sure that we have the correct version of rails:  
`$ rails -v`

If you've installed rails by yourself earlier, you might see "Rails 4.0.0". It would probably be better if you remove this for now using:  
`$ gem uninstall rails`

# Creating Our App

* `rails new cookbook`
* `cd cookbook`
* `rails generate scaffold Category name:string`
* `rails generate scaffold Recipe title:string description:string published_date:date instructions:text`

This much functionality was a week's worth of work once upon a time..

## Adding associations

In terminal :
`rails g migration AddCategoryIdToRecipes category_id:integer`

In recipe.rb add :  
`belongs_to :category`

In category.rb :
`has_many :recipes `

## Updating forms with association

- ### views/recipes/_form.html.erb

```

  <div class="field">
    <%= f.label :category_id, 'Category' %><br />
    <%= f.number_field :category_id %>
    <%= f.select(:category_id, Category.all.map {|c| [c.name, c.id] }) %>
  </div>
 ```

## Update views with association

- ### views/recipes/show.html.erb
  
```  
  <p>
    <b>Category:</b>
    <%= @recipe.category.name %>
  </p>
```

(ensure there's at least two recipes, and one has no category...)

- ### views/recipes/index.html.erb

`<%= @recipe.category.name %>`


Look at the error...


then change it to:  
  `<%= @recipe.category.name rescue nil %>`
  
Look at the error...


change it again to:  
 ` <%= @recipe.category.name if @recipe.category %>`
 
better.

and then change again to:  
 ` <%= @recipe.category.try(:name) %>`
 
### Pretty it up a little

- assets/stylesheets/application.css

```
  table {
    border-collapse:collapse;
    border:1px solid black;
  }

  table td {
    border:1px solid black;
  }
```

- Move the "show / edit / delete" buttons in views/recipes/index.html.erb


```
  <td><%= link_to(recipe.category.name, recipes_path(category_id: recipe.category.id)) if recipe.category %></td>
  <td>
      <%= link_to recipe.title, recipe %> 
      <%= link_to '(delete)', recipe, method: :delete, data: { confirm: 'Are you sure?' } %>
  </td>
```

- Move the "show / edit / delete" buttons in views/categories/index.html.erb

```

  <td>
    <%= link_to category.name, category %> 
    <%= link_to '(delete)', category, method: :delete, data: { confirm: 'Are you sure?' } %>
  </td>
```

### Templating. Add footers, etc.

- views/layouts/application.html.erb

```
  <!DOCTYPE html>
  <html>
  <head>
    <title>Online Cookbook</title>
    <%= stylesheet_link_tag    "application", :media => "all" %>
    <%= javascript_include_tag "application" %>
    <%= csrf_meta_tags %>
  </head>
  <body>
    <h1>Online Cookbook</h1>
    <%= yield %>

    <p>
      <% if controller_name == 'recipes' %>
         <%= link_to "Create new recipe", new_recipe_path %>
       <% else %>
         <%= link_to "Create new category", new_category_path %>
       <% end %>
     
       <%= link_to "Show all recipes", recipes_path %>
       <%= link_to "Show all categories", categories_path %>
     </p>

  </body>
  </html>
```

### Filtering in controller
- recipes_controller.rb#index

```

  @recipes = case
  when params[:category_id].nil?
    Recipe.all
  else
    Recipe.where(category_id: params.delete(:category_id))
  end
```

## Bundler

http://bundler.io/

Bundler is designed to maintain differents environments based on the "gemfile". Bundler is also maintaining the dependencies of all the gems. Because we use "rbenv", each rails command have to be run within a bundle scope. 

For this we use:

    $ bundle exec COMMAND

Each time we run a rails or rake command, we should use bundle exec. To make this easier, let's create some aliases (in our ".zshrc" file): 

    alias be="bundle exec" 
    alias rs="bundle exec rails s" 
    alias rc="bundle exec rails c"

## Rake

http://rake.rubyforge.org/

Rake is a task manager for ruby.

Rake is useful for running tasks that change our application's behavior or are part of the application ("personal rake tasks"). We can see all the rake tasks by typing `$ rake -T`

    $ rake assets:precompile : minify all assets files and unify them in one file
    $ rake db:create : run createdb
    $ rake db:migrate :
    $ rake db:migrate:down
    $ rake notes will display all notes beginning with [FIXME, OPTIMIZE, TODO]
    $ rake routesΩ

#ADDITIONAL RESOURCES


Introduction to Rails:  
<http://guides.rubyonrails.org/v3.2.13/>  
<http://guides.rubyonrails.org/routing.html>  
<http://asciicasts.com/episodes/203-routing-in-rails-3>  
<http://guides.rubyonrails.org/layouts_and_rendering.html>