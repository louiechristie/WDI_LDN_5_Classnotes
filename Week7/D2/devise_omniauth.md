# Devise

Devise is a flexible authentication solution for Rails.

It is composed of 11 modules:

* Database Authenticatable: encrypts and stores a password in the database to validate the authenticity of a user while signing in. The authentication can be done both through POST requests or HTTP Basic Authentication.

* Token Authenticatable: signs in a user based on an authentication token (also known as "single access token"). The token can be given both through query string or HTTP Basic Authentication.

* Omniauthable: adds Omniauth (https://github.com/intridea/omniauth) support;

* Confirmable: sends emails with confirmation instructions and verifies whether an account is already confirmed during sign in.

* Recoverable: resets the user password and sends reset instructions.

* Registerable: handles signing up users through a registration process, also allowing them to edit and destroy their account.

* Rememberable: manages generating and clearing a token for remembering the user from a saved cookie.

* Trackable: tracks sign in count, timestamps and IP address.

* Timeoutable: expires sessions that have no activity in a specified period of time.

* Validatable: provides validations of email and password. It's optional and can be customized, so you're able to define your own validations.

* Lockable: locks an account after a specified number of failed sign-in attempts. Can unlock via email or after a specified time period.



# Getting Started with Devise

  git@github.com:Pavling/wdi-w7d2-devise.git

### gemfile  
  `gem 'devise'`

Run the bundle command to install it.


After you install Devise and add it to your Gemfile, you need to run the generator:

  `rails generate devise:install`

The generator will install an initializer which describes ALL Devise's configuration options and you MUST take a look at it. 

It also lists some things you must do (and some things it recommends):  

  1. Ensure you have defined default url options in your environments files. Here is an example of default_url_options appropriate for a development environment in config/environments/development.rb:

    `config.action_mailer.default_url_options = { :host => 'localhost:3000' }`

    In production, :host should be set to the actual host of your application.

  2. Ensure you have defined root_url to *something* in your config/routes.rb.
     For example:

       `root to: "home#index"`

  3. Ensure you have flash messages appearing.
     For example:

      ` <p class="notice"><%= notice %></p>`  
       `<p class="alert"><%= alert %></p>`


When you are done, you are ready to add Devise to any of your models using the generator:

  `rails generate devise User`

Replace 'User' by the class name used for your application's users, it's frequently User but could also be Admin. This will create a model (if one does not exist) and configure it with default Devise modules. 


Have a look at the migration file that Devise has created...


  `rake db:migrate`

The generator also configures your config/routes.rb file to point to the Devise controller, and Devise will create some helpers to use inside your controllers and views.


Add accessible attributes for User:

 `attr_accessible :email, :password, :password_confirmation, :remember_me`


  
Let's make the contacts_controller require authentication...

  `before_filter :authenticate_user!`


To verify if a user is signed in, use the following helper:

  `user_signed_in?`


For the current signed-in user, this helper is available:

  `current_user`


After signing in a user, confirming the account or updating the password, Devise will look for a scoped root path to redirect. Example: For a :user resource, it will use user_root_path if it exists, otherwise default root_path will be used. This is what the root_path is needed for.

But you can also overwrite after_sign_in_path_for and after_sign_out_path_for to customize your redirect hooks.


### application_controller.rb
  
```
  protected
  def after_sign_in_path_for(resource)
    new_contact_path
  end
```

### application.html.erb

```
  <div class='login'>
  <% if current_user %>
    <%= current_user.email %>
    <%= link_to "Log out", destroy_user_session_path, method: :delete %>
  <% else %>
    <%= link_to "Sign In", new_user_session_path %>
    <%= link_to "Register", new_user_registration_path %>
  <% end %>
```


If you are deploying on Heroku with Rails 3.2, you may want to set:

       config.assets.initialize_on_precompile = false

     On config/application.rb forcing your application to not access the DB
     or load models when precompiling your assets.



If you want to customise the Devise views for your app:

       rails g devise:views



# Rails 3, Devise, Omniauth, and Google

 
Getting authentication through Google in a Rails application is a breeze with the right tools. To get a simple, no-frills authentication system up and running in a Rails 3 application, all you really need is Devise, Omniauth, and a Google API account.


- Step 1: Signing up for Google API access

  Before being able to wire up authentication in your rails app, you will need to set up a Google App. First, get your API key at:

  https://code.google.com/apis/console

  - Create a project
  - Click 'APIs & auth'
  - Make sure "Contacts API" and "Google+ API" are on.
  - Click on 'credentials' and provide details for your OAuth Client. For development purposes, the Home Page URL can be "localhost:3000"
  - Once you create a project, click on "API Access" and provide details for your OAuth Client. For development purposes, the Home Page URL can be "localhost:3000".

  After all of the details have been set, you will then get access to the screen with all of the information you will for your Rails app. For now, locate the section that contains your Client ID and Client Secret, you will need these later to configure your Rails application. Also make sure you have a redirect URI set to:

    http://localhost:3000/users/auth/google_oauth2/callback

  (unless you are using a different port or local server, in which case, use your computer's address)


- Step 2: Setting up your Rails app

  You will need to add the following to your app's gemfile:
    ```
    gem 'devise'
    gem 'omniauth-google-oauth2'
    ```
  bundle the new gems and then setup up devise from the command line:
    ```
    rails g devise:install
    rails g devise User
    ```
  Require authentication for the contacts controller
    ```
    # contacts_controller.rb
    before_filter :authenticate_user!
    ```
- Step 3: Configure the user model and Devise

  We will need to tweak the previous default Devise setup to get it to work with Google and OAuth. First, go into the user model and add the omniauthable package and attributes for :provider, and :uid (and any others you might want...)
    ```
    # add to migration
    t.string :provider
    t.string :uid
    ```
    
    ```
    # user.rb
    class User < ActiveRecord::Base
      devise :database_authenticatable, :registerable, :omniauthable,
             :recoverable, :rememberable, :trackable, :validatable,
             omniauth_providers: [:google_oauth2]

      attr_accessible :email, :provider, :uid, :password, :remember_me
    end
    ```

    ```
    # config/initializers/devise.rb
    config.omniauth :google_oauth2, 'APP_ID', 'APP_SECRET'
    ```

    Where 'APP_ID' and 'APP_SECRET' are replaced with your app's actual keys from step 1.


- Step 4: Setting up the Routes and Callback Controller

  Now that Devise is set up and your app is configured to work with Google correctly, it is time to work on the routes and controllers for handling the callbacks from Google. First, create a new controller called omniauth_callbacks_controller.rb and make it look something like this:

    ```
    # omniauth_callbacks_controller.rb
    class OmniauthCallbacksController < Devise::OmniauthCallbacksController
      def google_oauth2
        user = User.from_omniauth(request.env["omniauth.auth"])
        if user.persisted?
          flash.notice = "Signed in Through Google!"
          sign_in_and_redirect user
        else
          session["devise.user_attributes"] = user.attributes
          flash.notice = "Problem creating account"
          redirect_to new_user_registration_url
        end
      end
    end
    ```

  As you can see from the above code, the OmniauthCallbacksController has only the one 'google_oauth2′ method. This method instantiates a user from the information retrieved from the omniauth hash that came back from Google. It relies on the "from_omniauth" method that we will have to create on the User model in a moment, but for now it is important to understand that what this method does is, it checks for an existing user with the same credentials, if it finds one, it signs that user in, if it does not, then it redirects to Devise's new_user_registration_url to complete the registration process because this user does not yet exist.

  Next, adjusting the routes to handle this callback is as simple as adding the following to your routes file:

    `devise_for :users, controllers: { omniauth_callbacks: "omniauth_callbacks" }`


- Step 5: Finishing up the User Model

  We still need to handle the "from_omniauth" check necessary for the OmniauthCallbacksController in the User model.

    ```
    # user.rb 
    def self.from_omniauth(auth)
      if user = User.find_by_email(auth.info.email)
        user.provider = auth.provider
        user.uid = auth.uid
        user
      else
        where(auth.slice(:provider, :uid)).first_or_create do |user|
          user.provider = auth.provider
          user.uid = auth.uid
          user.email = auth.info.email
          user.password = Devise.friendly_token[0,20]
        end
      end
    end
    ```

  As you can see from the above code, I added one method to the user model to get all of the functionality working properly. The 'from_omniauth' method checks to see if a user exists based the on the information retrieved from the auth hash that Omniauth gives us. If a user already exits, the method returns the user and the controller then signs that user in.

  If that user does not yet exist, it creates a new user based on the information from Omniauth, and adds a default, random password to prevent errors from Devise.


- Step 6: Configuring the Views

  The final step in this process is to add the login, register, logout, and "sign in with Google" functionality. A basic solution to this is as simple as adding something like the following to your application layout file.

    ```
    <div class='login'>
      <% if current_user %>
        <%= current_user.email %>
        <%= link_to "Log out", destroy_user_session_path, method: :delete %>
      <% else %>
        <%= link_to "Sign In", new_user_session_path %>
        <%= link_to "Register", new_user_registration_path %>
        or
        <%= link_to "Sign in with Google", user_omniauth_authorize_path(:google_oauth2) %>
      <% end %>
    </div>
    <p class="notice"><%= notice%></p>
    <p class="alert"><%= alert%></p>
    ```


  You will have to restart your Rails app for all of the changes to take effect, but upon restart, you should now have a functioning Authentication system that lets users login either natively through your Rails app and Devise, or through Google.

  You should now see something like the following after firing up your local Rails Server:

  If a user clicks on "Sign in with Google", they will be redirected to to Google for authentication and upon success, redirected back to your application. Yay!


# Limitations and Considerations

  This app makes an assumption that would need to be overcome if your needs are different. Currently, this app would only work with either Devise or Google OAuth for authentication. If you would like other methods (like Facebook, Twitter, or Github) in addition to Google, you would need to write methods to handle the various callbacks from each of those respective services.



