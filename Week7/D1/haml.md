#HAML


Haml (HTML Abstraction Markup Language) is based on one primary principle: markup should be beautiful. Its purpose is to help us write HTML in a cleaner, faster, and more organized way. It is a better, DRYer version of HTML.

As a lightweight markup language, it is used to describe the XHTML of any web document without the use of traditional inline coding. 

We can use Haml in place of our traditional ".erb" templates. 

Careful though: Haml is an "white-space sensitive" - and line indentation has meaning. Mistakes in indentation can break your code. 


## Getting started
Starting a rails app, the usual way

Let's create a new rails app:

    git@github.com:Pavling/wdi-w7d1-haml_and_sass.git

    $ rails new haml_app

We can download the haml gem using the command:

Start by removing the "public/index.html" file. 

We already have our layout, in "views/layout/application.html.erb":

    <!DOCTYPE html>
    <html>
      <head>
        <title>HTML is soooo 80's</title>
        <%= stylesheet_link_tag "application", :media => "all" %>
        <%= javascript_include_tag "application" %>
        <%= csrf_meta_tags %>
      </head>
      <body>
        <%= yield %>
      </body>
    </html

We then need to create a route for the home page, and we'll create one for a HAML verison.
In "config/routes.rb":

    get "/", to: "home#index"
    get "/haml", to: "home#index_haml"

We can now create the HomeController with an empty index action.

In "app/controllers", we create our new "home_controller.rb":

    class HomeController < ApplicationController
      def index
        @text = "Hello, WDI!"
      end

      def index_haml
        @text = "Hello, WDI, from the HAML action!"
      end
    end

Let's create a "home" folder inside "app/views". In there, we can now add an index view file. 

In "/views/home/index.html.erb":

    <div id="div_1">
      <p>DIV 1</p>

      <div id="div_2">
        DIV 2
      </div>

      <div id="div_3">
        DIV 3
        <p><%= @text %></p>
      </div>
    </div>

    <div id="div_4">
      DIV 4
    </div>

A quick look at the app running in Chrome:

... it shows us that it is in great need of styling.

Let's add a stylesheet! 

In "app/assets/stylesheets/", we can add our "style.css" file:

    body {
      background-color: lightblue;
    }
    #content { 
      background-color: red;
      color: blue;
    }
    #footer {
      background-color: silver;
      color: blue;
    }
    div {
      border: 1px solid black;
      color: white;
      margin: 10px;
      padding: 10px;
      width: 256px;
    }
    #div_1 {
      background-color: black;
      width: 300px;
    }
    #div_2 {
      background-color: yellow;
    }
    #div_3 {
      background-color: blue;
    }
    #div_4 {
      background-color: red;
    }

Running this in Chrome, we can see that our styling has been applied. However, a quick glance at our files makes it quite obvious that there is a lot of code typed in there just for the structure of the page. 

HTML uses a quite tedious, complex syntax, and is all about repetitions of characters. It not very DRY (with , and most importantly, it can be hard to read and to maintain.

That's where Haml comes in handy.


## Our first foray into Haml

To use HAML, you need to include `gem 'haml'` in your app - but because this is a Rails app, we'll use a more functional gem in our Gemfile:

    gem 'haml-rails'
    
Using this, Rails will generate HAML views when with scaffold, etc.

.. and then bundle

Inside of our "views/home" folder, let's create a new view, "index_haml.html.haml".

Now let's rewrite the exact same document, but we'll be using Haml syntax instead of the HTML structure we're familiar with.

In "index_haml.html.haml":

    %div#div_1
      %p
        DIV 1 FROM HAML
      %div#div_2
        DIV 2
      %div#div_3
        DIV 3
        %p
          =@text

    %div#div_4
      DIV 4

Notice that closing tags are gone in Haml. We open tags by using % followed by the name of the tag. 

Instead of a closing tag, indentation tells us which elements are inside these tags. Everything that's indented UNDER that %tag_name will be considered as being inside to that particular element.

-- Again, INDENTATION is key - pay very close attention to this when writing code. If you chose to indent with TWO space, keep the same amount of spaces throughout. The convention is TWO spaces.


## Ruby in HAML

Our old ERB syntax of `<%= #some ruby code %>` and `<% #some ruby code %>` are very similar in HAML. Given that we're using the '%' to indicate an HTML element, we can use just the '=' (or a '-' for the equivalent of 'no puts')


## Conditional statements

Ruby blocks translate with the same syntax as plain old Ruby code - but indentation highlights the end of the block, so `<% end %>` tags can be ommitted:

    <div id="div_4">
      DIV 4
      <% if @text == "y u no" %>
        <%= @text.upcase %>
      <% end %>
    </div>

...can translate to:

    %div#div_4
      DIV 4
      - if @text == "y u no" -# the " - " is equivalent to a <%%> tag without =
        %p
          = @text.upcase -# will only be printed if the condition is true


## Looping

We can add in loops and other blocks too:

    #div_4
      DIV 4
      - if @text == "y u no"
        %p
          = @text.upcase
      - 7.times do |number| -# adding the loop
        %p = number
      %p INLINE TEXT 


## CSS selectors

In our stylesheet, let's add a new class:

    .text-right {
      text-align: right;
    }

In "index_haml.html.haml", we can add that class to div_3:

    %div#div_3.text-right -# for a class, we just need a .
      DIV 3
      %p
        =@text 

    %div#div_4 -# for an ID, we just need #
      DIV 4 


## Shortcuts

Even easier, we can skip typing %div altogether, and just specify the ID or the class of that particular div. HAML will create a "div" element by default if no other element is given.
Example:

    #div_1
      %p
        DIV 1 FROM HAML
      
      #div_3.text-right
        DIV 3
        %p
          =@text


## HTML element attributes

We can build forms using Haml. 

Let's add a form to our DIV 2:
  %div#div_2
    DIV 2
    %form{method: :post, action: "/result"}
      %input{type: :text, placeholder: :Username}
      %input{type: :submit, value: :Send}  

The "hash" of parameters passed to the HAML elements gets translated into HTML attributes.


## Converting a file:

We can take our layout, in "views/layout/application.html.erb":

    <!DOCTYPE html>
    <html>
      <head>
        <title>HTML is soooo 80's</title>
        <%= stylesheet_link_tag "application", :media => "all" %>
        <%= javascript_include_tag "application" %>
        <%= csrf_meta_tags %>
      </head>
      <body>
        <%= yield %>
      </body>
    </html

...and convert it in Haml.

To that effect, let's create an "application.html.haml" file:

    !!! 5  -# tells the browser to interpret what follows as HTML5
    %html
      %head
        %title HamlApp
        = stylesheet_link_tag "application", media: "all"
        = javascript_include_tag "application"
        = csrg_meta_tags
      %body
        = yield

All that's left is to rename our "application.html.erb" file into "old_application.html.erb", and 

If we now run this in the browser, then right-click to "View page source", we can see that the source code is HTML. This is because Haml is compiled into HTML, to allow the browser to read it. 


More practice: let's convert this code! (`/app/views/home/haml_practice.html.erb`)

```
<!DOCTYPE html>
<html>
  <head>
    <title>HAML Practice</title>
  </head>
  <body>
    <div id='wrapper'>
      <div id='header'>
        <ul class='menu'>
          <% %w(Home Contact About).each do |menu_item| %>
            <%= content_tag :li, class: 'menu-item' do %>
              <%= menu_item %>
            <% end %>
          <% end %>
        </ul>
      </div>
      <div id='sidebar'>
        <div class='favourites'>My faves</div>
        <div class='todo-list'>Must do</div>
      </div>
      <div id='content'>
        <form action='/postform' method='post'>
          <input name='name' placeholder='Name' type='text'>
          <input name='email' placeholder='Email' type='text'>
          <label>Sex</label>
          <select>
            <option value='f'>Female</option>
            <option value='m'>Male</option>
          </select>
        </form>
      </div>
      <div id='footer'>
        <p>Copyright 2013</p>
      </div>
    </div>
  </body>
</html>
```

...can be converted into:

```
!!! 5
%html
  %head
    %title HamlLab
  %body
    #wrapper
      #header
        %ul.menu
          -%w(Home Contact About).each do |menu_item|
            = content_tag :li, class: 'menu-item' do
              = menu_item
      #sidebar
        .favourites
        .todo-list
      #content
        %form{method: :post, action: "/postform"}
          %input{type: :text, placeholder: :Name, name: :name}
          %input{type: :text, placeholder: :Email, name: :email}
          %label Sex
          %select
            %option{value: :f} Female
            %option{value: :m} Male
      #footer
        %p Copyright 2013
```

http://haml.info/docs/yardoc/file.REFERENCE.html
http://www.cheatography.com/specialbrand/cheat-sheets/haml/