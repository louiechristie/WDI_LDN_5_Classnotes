# Authentication


We are going to learn about making our site more secure. The following example, very basic, uses a fictional app (and yes, these are .erb and html files!), and will help illustrate and frame these new concepts. 

  - Authentication is about making sure you know the identity of the person accessing your site.

  - Essentially, it's about asking for passwords, or other proof of identity.

  - but it doesn't guarentee anything... my wife knows my email password, so could "pretend" to be me


###HTTP Basic Authentication

Password-protected actions are a common feature in most web applications, only allowing registered users in with a valid password. 

This is called "User Authentication".

HTTP basic Authentication is the simplest possible authentication system available. 
→ the user cannot proceed until they enter a username and a password. 

It’s supported by all browsers, and you don’t need to build a discrete login page. 

This is not an incredibly secure solution, because Base64 is not an encryption algorithm. 

The encoding is intended to strip away characters that are not compatible with HTTP. That said, anyone who can see the traffic can decrypt the username and password. 

This makes auto basic a bad candidate for secure websites over vanilla HTTP. However, if the site is on HTTPS, this remains a good candidate.


## create an app to play with authentication

`rails new authentication`

#### HTTP Basic
  
- A very simple form of authentication, which doesn't offer any confidentiality of transmitted credentials. So generally, you'd want to add HTTPS to this as a second layer of security.


First let’s generate two controllers, one to require authentication, the other will not.  
`$ rails g controller secret index`
`$ rails g controller gossip index`


Now we will update our routes.  
`get '/secret', to: 'secret#index' `
`get '/gossip', to: 'gossip#index'`  


Now we can set up our controllers. 

In "secret_controller.rb":  

```
class SecretController < ApplicationController
 def index
  render text: "shh its a secret"
 end
end
```

In "gossip_controller.rb":  

```
class GossipController < ApplicationController
 def index
  render text: "Did you hear?"
 end
end
```

As above, there's nothing protecting my secrets...


We will change our secret controller to include HTTP basic authentication. 

```
class SecretController < ApplicationController
 http_basic_authentication_with name:'secret', password:'password'
 def index
  render text: "shh its a secret"
 end
end
```

There are a few drawbacks to this method of authentication:   

- firstly, we are hardcoding our authentication, 
- secondly, as this is HTTP, the username and password will be passed in the open.

Now when we visit our secret page we will need to sign in to view this page. Once signed in, our session will be stored and we will be able to go to another page, come back and still be signed in. 

Lets add another page with double authentication. This will require us to login twice, once to get to secret, and another time to get to really secret.

In "routes.rb":  
`get '/secreter', to: 'secret#really_secret'`


In "secret_controller.rb":  

```
class SecretController < ApplicationController
 http_basic_authentication_with name:'secret', password:'password', except: :really_secret
 http_basic_authentication_with name:'more secret', password:'password1', only: :really_secret
 def index
  render "shh its a secret"
 end
 def really_secret
  render text: "DO NOT TALK ABOUT WDI"
 end
end
```

- if you add it to the application_controller it would apply to every controller

- This is a very quick and easy way of restricting access by password

---

##Secure password authentication


"has_secure_password" is an authentication system which uses bcrypt hashing and SSL. 

SSl, or "Secure Sockets Layer", is a standard security technology for establishing an encrypted link between a server and a client — typically a web server (website) and a browser, or a mail server and a mail client (e.g., Outlook).

"has_secure_password" gives you:   

- password hashing and salting, 
- authenticating against the hashed password, 
- and password confirmation validation.

We will first need to install the bcrypt encryption gem. In our gemfile, we add the line:  
`gem 'bcrypt-ruby', '~> 3.0.0'`

Then in terminal:  
`$ bundle`
`rbenv rehash`

Now we can generate a User model with an email and pasword_digest.
`$ rails g model User email password_digest`
`rake db:migrate`

In "models/user.rb":  

```
class User < ActiveRecord::Base
 has_secure_password
 validates :email, presence: true, uniquness: true
 attr_accessible :email, :password, :password_confirmation
end
```

...and generate a users controller:  
`$ rails g controller users index new create`

In "controllers/users_controller.rb":  

```
class UsersController < ApplicationController
 def index
  @users = User.all
 end
 def new
  @user = User.new
 end
 def create 
  @user = User.new(params[:user])
  if @user.save
   redirect_to users_path
  else
   render 'new'
  end
 end
end
```
Let’s update our routes to include our users.  
`$ resources :users`

Then we update our views.

In "views/users/index.html.erb":

```

  <h1> Users index </h1>
  <% @users.each do |user|%>
   <%= user.email %>
   <%= user.password_digest %>
  <% end %>

```

In "views/users/new.html.erb":  

```
  <h1>Sign Up</h1>
  <%= form_for @user do |f| %>
    <% if @user.errors.any? %>
      <div class="error_messages">
        <h2>Form is invalid</h2>
        <ul>
          <% for message in @user.errors.full_messages %>
            <li><%= message %></li>
          <% end %>
        </ul>
      </div>
    <% end %>
    <div class="field">
      <%= f.label :email %>
      <%= f.text_field :email %>
    </div>
    <div class="field">
      <%= f.label :password %>
      <%= f.password_field :password %>
    </div>
    <div class="field">
      <%= f.label :password_confirmation %>
      <%= f.password_field :password_confirmation %>
    </div>
    <div class="actions"><%= f.submit %></div>
  <% end %>
```

In "views/application/layout.html.erb", at the top of our body section, add:  

```
  <% flash.each do |name, message| %>
   <div class="flash-message flash-message-<%= name %>">
    <%= message %>
   </div>
  <% end %>
```

Now to allow the user to login and out, we will need to create a sessions controller.  
`$ rails g controller sessions new create destroy`

Now we can create routes for this controller. 


In "routes.rb":  

```
root to: "users#index"
get 'login', to: 'sessions#new'
resources :sessions, only: [:new, :create, :destroy]
```

In "sessions_controller.rb":  

```
class SessionsController < ApplicationController
 def new 
 end
 def create 
  user = User.find_by_email(params[:email])
  if user && user.authenticate(params[:password])
   session[:user_id] = user.id
   redirect_to root_path, notice: "logged in!"
  else
   flash.now.alert = "invalid login credentials"
   render "new"
  end
 end
 def destroy
  session[:user_id] = nil
  redirect_to root_url, notice: "logged out!"
 end
end
```
Now we will need to add a login form.  

In "views/sessions/new.html.erb":

```
  <h1>Login</h1>
  <%= form_tag sessions_path do %>
    <div class="field">
      <%= label_tag :email %>
      <%= text_field_tag :email, params[:email] %>
    </div>
    <div class="field">
      <%= label_tag :password %>
      <%= password_field_tag :password %>
    </div>
    <div class="actions"><%= submit_tag "Log in" %></div>
  <% end %>
```


We will need to create a method within our application controller which will assign the "current_user":

```
class ApplicationController < ActionController::Base
  helper_method :current_user
  def current_user
    @current_user ||= User.find(session[:user_id]) if session[:user_id]
  end
  def logged_in?
    !!current_user
  end
  private
  def authenticate
    unless logged_in?
      flash[:error] = "You must be logged in to access this section of the site"
      redirect_to login_url
    end
  end
end
```

Now in our application layout, we can use "current_user". At the top of the body section include:

```
  <% if current_user %>
   <p> hello, <%= current_user.email %> </p>
   <%= link_to 'Logout', sessions_path(session), method: :delete, data: {confirm: ‘Are you sure?’} %>
  <% else %>
   <%= link_to 'Please login', login_path %>
  <% end %>
```

We can now update our SecretController to use our new authentication: 

```
class SecretController < ApplicationController
 before_filter :authenticate
 def index
  render text: "shh its a secret"
 end
end
```


##### sessions_controller.rb
force_ssl # doesn't force in development, only production

rake assets:precompile  
- change database.yml to use dev db in production  
rails s -e production

 - it will break, as ssl isn't working in production on our local machines (but if we push to heroku...)  
 Alternatively... if you feel ambitious:   
   <http://www.railway.at/2013/02/12/using-ssl-in-your-local-rails-environment/>

- You are not able to decrypt a user's password, so if they forget it, you would need some mechanism to reset it.

- We can also use gems that give us authentication and extra functionality like password reseting, inviting friends, and integration with other authorisation (like Gmail, or Facebook)



- - -

Example:


Now that we’ve seen quite a basic example with a mock app, let’s dive into authentication within the context of our CookBook app. 

There are three steps to authentication:  

- sign-up
- sign-in
- sign-out

They’re all based on a model, usually, “User”.

Looking at the app from this morning, we can start by adding the matching routes to these bits of functionality.

For starters, let’s make sure we comment out the `before_filters`, `after_filters` and `around_filters` in the “recipes_controller.rb” (but keep the `before_filter` `:find_record` line).

Then, in “config/routes.rb”, we can add (see at the bottom):  
`R20130214Cookbook::Application.routes.draw do`  
`  resources :ingredients`

```
  # get    '/recipes',          to: 'recipes#index',  as: :recipes
  # post   '/recipes',          to: 'recipes#create'
  # get    '/recipes/new',      to: 'recipes#new',    as: :new_recipe
  # get    '/recipes/:id/edit', to: 'recipes#edit',   as: :edit_recipe
  # get    '/recipes/:id',      to: 'recipes#show',   as: :recipe
  # put    '/recipes/:id',      to: 'recipes#update'
  # delete '/recipes/:id',      to: 'recipes#destroy'
  # post   '/recipes/:recipe_id/quantities',     to: 'quantities#create', as: :recipe_quantities
  # get    '/recipes/:recipe_id/quantities/new', to: 'quantities#new',    as: :new_recipe_quantity
  # delete '/recipes/:recipe_id/quantities/:id', to: 'quantities#destroy' as: :recipe_quantity
```

```
  resources :recipes do
    member do
      put :flag
    end
    collection do
      get :flagged
    end
    resources :quantities, only: [:new, :destroy, :create]
  end
```

`  root to:  "recipes#index"`

```
  get “/signup”, to: “users#new”,        as: “signup”
  get “/login”,  to: “sessions#new”,     as: “login”
  delete “/logout”, to: “sessions#destroy”, as: “logout”
end
```

Now if we `$ rake routes`, we have access to these three new routes.

- - -

Let’s create the “users_controller.rb”:  

```
class UsersController < ApplicationController
  def index
  end

  def new
  end

  def create
  end
end
```

..and the “sessions_controller.rb”:  

```
class SessionsController < ApplicationController
  def new
  end

  def create
  end

  def destroy
  end
end
```

- - -

We can now create the model, “user.rb”:  
`class User < ActiveRecord::Base`  
`end`

We also a need a migration to allow users to be set in the database. In terminal:  
`$ rake g migration CreateUsers`  

And edit the resulting migration:  

```
class CreateUsers < ActiveRecord::Migration
  def up
    create_table :users do |t|
      t.string :name
      t.string :email
      t.string :password_digest
      t.string :role
    end
  end
```

```
  def down
    drop_table :users
  end
end
```
- - -

Back into the gemfile, let’s add the gem:   
`gem 'bcrypt-ruby', '~> 3.0.0'`

- - -

Then, in the User model:  

```
class User < ActiveRecord::Base
  has_secure_password
  attr_accessible :email, :name, :role, :password, :password_confirmation
end
```

We can go into the console, and try and assign a password to a user.  
`$ rails c`  
`$ user = User.new`  
`$ user.name = “Julien”`  
`$ user.email = “julien@email.com”`  
`$ user.password = “password”`  
`$ user.password_confirmation = “password”`  
`$ user.save`  
Now, if we check out this user:  
`$ User.first`  
⇒ we see the password has been encrypted (password_digest)!  
`$ user = User.first`  
`$ user.authenticate “password”`  
⇒ returns the user info.   
`$ user.authenticate “incorrect password”`  
`#=> false`  

- - -

Back in Sublime, let’s create some views.

In our “views” folder, let’s add a “users” folder. In it, add a “new.html.haml” file: 
 
```
<%= form_for(@user) do |f| %>
  <div class="field">
    <%= f.label :name %>
    <%= f.text_field :name %>
    <br/>
    <%= f.label :email %>
    <%= f.text_field :email %>
    <br/>
    <%= f.label :password %>
    <%= f.password_field :password %>
    <br/>
    <%= f.label :password_confirmation %>
    <%= f.password_field :password_confirmation %>
    <br/>
    <%= f.submit   %>
  </div>
<% end %>

```

And in our routes.rb, let’s add:  

```
  resources :users, only: [:index, :new, :create]
  resources :sessions, only: [:new, :create, :destroy]
```

You should now see a signup page (with a signup form) at the corresponding url!

- - -

In our “users_controller.rb”, let’s flesh out the methods we outlined earlier:  

```
class UsersController < ApplicationController
  def index
  end

  def new
  end

  def create
    @user = User.new params[:user]
      if @user.save
        session[:user_id] = @user.id
        redirect_to root_path, notice: "Thank you for signing up!"
      else
        render :new
    end
  end
end
```

In our “new.html.haml”, we can add, at the top:  

```
<% if @user.errors.any? %>
  <div class="error-messages">
    <h2>Form is invalid</h2>
    <ul>
      <% @user.errors.full_messages.each do |message| %>
        <li>
          <%= message %>
        </li>
      <% end %>
    </ul>
  </div>
<% end %>
<%= form_for(@user) do |f| %>
  <div class="field">
    <%= f.label :name %>
    <%= f.text_field :name %>
    <br/>
    <%= f.label :email %>
    <%= f.text_field :email %>
    <br/>
    <%= f.label :password %>
    <%= f.password_field :password %>
    <br/>
    <%= f.label :password_confirmation %>
    <%= f.password_field :password_confirmation %>
    <br/>
    <%= f.submit   %>
  </div>
<% end %>
```


And in our “user.rb”:  

```
class User < ActiveRecord::Base
  has_secure_password
  validates :name, presence: true
  validates :email, presence: true, uniqueness: true
  attr_accessible :email, :name, :role, :password, :password_confirmation
end
```

Back to Chrome… we can now sign up!

- - - 


In our “app/views” folder, let’s create a “sessions” folder. In it, add a “new.html.haml” file:

```
  <h1>Login</h1>
  <%= form_tag sessions_path do %>
    <div class="field">
      <%= label_tag :email %>
      <br/>
      <%= email_field_tag :email %>
    </div>
    <div class="field">
      <%= label_tag :password %>
      <br/>
      <%= password_field_tag :password %>
    </div>
    <div class="actions">
      <%= submit_tag "Login!" %>
    </div>
  <% end %>
```
    
    
- - -

Back into the sessions_controller.rb:

```
class SessionsController < ApplicationController
  def new
  end

  def create
    user = User.find_by_email params[:email]
    if user && user.authenticate(params[:password])
      session[:user_id] = user.id
      redirect_to root_path      , notice: "Logged in!"
    else
      flash.now.alert = "Invalid login credentionals"
      render :new
    end
  end

  def destroy
  end
end
```

We now have working signup and signin routes. On to the sign-out!

- - -

Inside our “application_controller.rb”:

```
class ApplicationController < ActionController::Base
  protect_from_forgery
  helper_method :current_user

  def current_user
    @current_user ||= User.find(session[:user_id]) if session[:user_id]
  end
  def logged_in?
    !!current_user
  end
end
```

→ notice the `helper_method`: the method `helper_method` is to explicitly share some methods defined in the controller to make them available for the view. This is used for any method that you need to access from both controllers and helpers/views (standard helper methods are not available in controllers). “current_user” is a very common use case.

→ in `!!current_user`, the `!!` is basically calling the inverse of the inverse of the value, but returns a boolean, always. Current_user returns “nil” or an object. If it returns an object, the `!!current_user` will return false. If current_user returns “nil”, `!!current_user` will return true.

- - -

And now, in our layout, application.html.haml:  

```
<!DOCTYPE html>
<html>
  <head>
    <title>Cookbook</title>
    <%= stylesheet_link_tag    "application", :media => "all" %>
    <%= javascript_include_tag "application" %>
    <%= csrf_meta_tags %>
  </head>
  <body>
    <div id="page">
      <nav class="navbar">
        <ul class="left-nav">
          <li>
            <%= link_to('Recipes', recipes_path) %>
          </li>
          <li>
            <%= link_to('Ingredients', ingredients_path) %>
          </li>
        </ul>
        <ul class="right-nav">
          <li>
            <% if current_user %>
              logged in as #{current_user.name},
              <%= link_to 'Logout', logout_path, method: :delete %>
            <% else %>
              <%= link_to "Login", login_path %>
              or
              <%= link_to "Sign up", signup_path %>
            <% end %>
          </li>
        </ul>
        <div class="clear"></div>
      </nav>
      <% flash.each do |name, message| %>
        <div>(class="flash-message flash-#{name}")
          <%= message %>
        </div>
      <% end %>
      <section id="main">
        <%= yield %>
      </section>
    </div>
  </body>
</html>
```

- - - 

Now, in our “sessions_controller.rb”:  

```
class SessionsController < ApplicationController
  def new
  end

  def create
    user = User.find_by_email params[:email]
    if user && user.authenticate(params[:password])
      session[:user_id] = user.id
      redirect_to root_path, notice: "Logged in!"
    else
      flash.now.alert = "Invalid login credentionals"
      render :new
    end
  end

  def destroy
    session[:user_id] = nil
    redirect_to root_url, notice: "You've been logged out."
  end
end
```


This is how we handle authentication in rails!