#HIGH VOLTAGE

git clone `git@github.com:JAdshead/cookbook_high_voltage.git`

High voltage greatly simplifies the process of creating static pages (such as "about", "legal", "faq" pages, etc.).

Source:  
<https://github.com/thoughtbot/high_voltage>


##Setting things up

To illustrate this new gem, we will work on another version of the cookbook app we're now quite familiar with.

Download the app sent by Gerry on hipchat, unzip it, and in the terminal: 
 
`$ bundle`  
`$ rake db:drop`  
`$ rake db:setup`  

- - -

In the gemfile, we then need to add:   
`gem 'high_voltage'`

We are also going to use haml-rails
`gem 'haml-rails'`

Then, in terminal:  
`$ bundle `  

- - -

### Adding a static page

From this point onward, we can add static pages to our app just by creating templates in "app/views/pages".

Let's start with a simple "about" page.

In terminal:  
`$ mkdir app/views/pages`  
`$ touch app/views/pages/about.html.haml`  
`$ rails s`  

We can now add static content to the "about.html.haml" template:  

```
%h1 About
%p
  Lorem ipsum dolor sit amet, consectetur adipiscing elit. In id fringilla nisi. Curabitur nisi lacus, suscipit quis mauris ac, aliquam suscipit ipsum. Pellentesque dui tortor, rhoncus eget cursus vel, porta vel enim. Vestibulum ante ipsum primis in faucibus orci luctus et ultrices posuere cubilia Curae; Sed dapibus tincidunt nisl sodales fermentum. Nam quis ligula sit amet eros venenatis porta at a orci. Aliquam a dictum tortor, a lobortis nisi. Praesent tincidunt varius malesuada. Pellentesque luctus porttitor blandit. Praesent rhoncus justo odio, id ultrices ante feugiat a. Nulla tincidunt lacus eu fringilla sodales.
```

We can access this page online at <http://localhost:3000/pages/about>

- - -

### Linking

To link to this page in the application, we can use high voltage's "page_path" helper, with the name of the page as an argument.

In "application.html.haml", after the "yield", we can add:

```
   %footer
     = link_to 'About', page_path('about')
```
- - -

### Nesting


Another useful feature of high voltage: nesting.

We can nest our static pages in a directory structure:  
`$ mkdir app/views/pages/legal_mentions`  
`$ touch app/views/pages/legal_mentions/conditions.html.haml`

In "conditions.html.haml", add:  
`%h1 Legal Mentions`  

And in "application.html.haml", in the footer section, we can now add:    
`  = link_to 'conditions', page_path('legal_mentions/conditions')`

- - - 


###Meta Content

Conveniently, we can still use ruby inside static templates. By using the "content_for" helper, we can change content on the layout.

For example, let's override the page title that is displayed on Chrome's tab.

In "application.html.haml", edit the "%title" contained in the "head" div:  

```
%html
  %head
    %title= (yield(:title).empty? ? "Cookbook" : yield(:title) )
     …..
```

We can then add the following code to our "about.html.haml" page:  
`= content_for :title, "About us"`  

→ the title of our Chrome tab will now change to "About us" when we are visiting that page.


###Top level 

By default , high voltage use "/pages" in the url to dynamically create a path to our static templates. We can change this by setting up a new initializer.

In "config/initializers", create a "high_voltage.rb" file. 

Then, in "config/initializers/high_voltage.rb", add:  
`HighVoltage.route_drawer = HighVoltage::RouteDrawers::Root`

We can now access our pages at:  
`http://localhost:3000/about`  

...and:  
`http://localhost:3000/legal_mentions/conditions`

→ notice that we don't need to mention "/pages/" in our path anymore.


###Root as static content:  

By default, high voltage will use "page/:id" to route to the template - but it can be customized.

For instance, if we want to use a static page as our homepage, add in "routes.rb":  
 ` root :to => 'high_voltage/pages#show', id: 'about'`

<!--

We can also establish the "root to:" as a static page. 

To that effect, in "routes.rb", we can comment out:  
  `root to:  "recipes#index"`

...and replace it with:
`  root to: 'high_voltage/pages#show', id: 'about'`

-->
###Overriding the Controller


Sometimes, we might need to override the abstract high voltage controller. 

In most cases, it might be because we need authentication on these static pages, or because we need to change the default layout.

- - -

To override the controller, start by creating a "pages_controller.rb":  
`$ rails g controller pages`

- - - 

We also need to update the initializer.

Inside "config/initializers/high_voltage.rb", add:  
`  HighVoltage.routes = false`

Then, inside "app/controllers/pages_controller.rb", add:

```
class PagesController < ApplicationController
  include HighVoltage::StaticPage
end
```

→ the "show" method, which was missing in our "pages_controller.rb", is included in the "StaticPage" class.

-

Instead of redirecting requests to static pages (via the high voltage default controller), we need to redirect them via our "pages_controller.rb". 

To do this, in our "routes.rb" file, add:  
`  get "/pages/*id" => 'pages#show', as: :page, format: false`

- - -

If we still want to use the root as a static content page, we also need to redirect.

We can thus change:  
`  root :to => 'high_voltage/pages#show', id: 'about'`

...to:  
`  root :to => 'pages#show', id: 'about'`
