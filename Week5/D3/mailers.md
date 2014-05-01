# Mailers 

## Action Mailer 

Action Mailer allows you to send emails from your application using mailer classes and views.

In rails mailers work very similarly to controllers. They inherit from ActionMailer::Base and live in app/mailers, and have associated views that appear in app/views.

    $ git clone git@github.com:Pavling/wdi-w5d3-mailer.git
    $ cd wdi-w5d3-mailer
    $ bundle install
    $ rake db:drop
    $ rake db:setup


Now we can generate a mailer.

    rails g mailer UserMailer

This will create a user_mailer class within the app/mailers directory.
It will also generate a user_mailer view directory for us. 

Now if we go into our user mailer class we will see some code has been generated for us:

    class UserMailer < ActionMailer::Base
      default :from => "from@example.com"
    end

We will update this. We will add methods for each different type of email we want to send. We will update it to send a registration_confirmation email. The argument is the user, who we are going to send it to. 

We have a default at the top, which will tell mailer that all methods will have the default options. 

    class UserMailer < ActionMailer::Base
      default :from => "from@example.com"

      def registration_confirmation(user)
        mail(:to => user.email, :subject => "Registered")
      end
    end


Now we will need to create a view for this email under user_mailer, create a file called registration_confirmation.text.erb.

    touch app/views/user_mailer/registration_confirmation.text.erb

This will be our plain text email:

    Thank you for registering!

Now we will need to update our controller to actually send the email. We will do this in our UsersController:

So we will only want this email to be sent to a user after they have registered with our site. So we will put our mailer code into the create action of our users controller. So once the user has been saved successfully we write:

    UserMailer.registration_confirmation(@user).deliver

We are passing the argument @user, to which the registration_confirmation will be sent to.

### CHECK EMAIL

## 'letter_opener' gem

First add the gem to your development environment and run the bundle command to install it.

    gem "letter_opener", :group => :development

Then set the delivery method in config/environments/development.rb

    config.action_mailer.delivery_method = :letter_opener

Now any email will pop up in your browser instead of being sent. 
The messages are stored in tmp/letter_opener.


## Improve our email

At the moment our email is not very interesting. We really want to take some of the users information and pass it into our user_mailer view. To do this we will go into the UserMailer class and add some more code to our registration_confirmation action. 

    class UserMailer < ActionMailer::Base
      default :from => "from@example.com"
      
      def registration_confirmation(user)
        @user = user
        mail(:to => user.email, :subject => "Registered")
      end
    end


Now we will have access to our user within our view. We can now change this view to:

    Welcome <%= @user.name %>, thank you for registering!
    Edit profile: <%= edit_user_url(@user) %>

We can also make a html view, to send a html email, rather than just a plain text we will create a new file called registration_confirmation.html.erb

    touch app/views/user_mailer/registration_confirmation.html.erb

    <p>Welcome <%= @user.name %>, thank you for registering!</p>
    <p><%= link_to "Edit profile", edit_user_url(@user) %></p>

This is a multipart email, so it will render the plain text and html version of the email.

## Attachments
We can also add attachments to our email. We will do this again in the UserMailer class, in our registration_confirmation action. 

    class UserMailer < ActionMailer::Base
      default :from => "ryan@railscasts.com"
      
      def registration_confirmation(user)
        @user = user
        attachments["rails.png"] = File.read("#{Rails.root}/app/assets/images/rails.png")
        mail(:to => "#{user.name} <#{user.email}>", :subject => "Registered")
      end
    end



# Mandrill

For simple testing of email, it's easy to use Gmail to send email messages from the application. For deployment, when the application must send dozens or thousands of acknowledgments or invitations, you will need a hosted SMTP(Simple mail transport protocol) relay service (also known as an ESP or "email service provider"). For this we will use Mandrill.

Go to mandrill website and sign up: https://mandrill.com/signup/

Configuring our environment to use Mandrill

We will need to configure our application for our email account.

config/environments/production.rb:

    config.action_mailer.default_url_options = { :host => 'example.com' }
    # ActionMailer Config
    # Setup for production - deliveries, no errors raised
    config.action_mailer.delivery_method = :smtp
    config.action_mailer.perform_deliveries = true
    config.action_mailer.raise_delivery_errors = false
    config.action_mailer.default :charset => "utf-8"

    config.action_mailer.smtp_settings = {
      :address   => "smtp.mandrillapp.com",
      :port      => 25,
      :user_name => "MANDRILL_USERNAME",
      :password  => "MANDRILL_API_KEY"
    }

We will need to change the username and api key to match those provided on Mandrill. 



## Deploy to heroku 

Now we will run heroku create.

    $ heroku create

change host on production.rb file to match heroku

    $ git add .
    $ git commit -m "finished mailer"

We can now push our application to heroku.

    $ git push heroku master

We will now need to migrate our database on heroku.

    $ heroku run rake db:migrate

Now to view our webpage in the browser we can run.

    $ heroku open
