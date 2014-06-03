
# Delayed jobs with Sidekiq


Sometimes our web app must do some slow processing in response to a user action, like these:
send a confirmation email
process and upload an image to S3

The problems is that this slow code in our controller action usually means a long wait for the user.

To avoid this, we can add the slow processes to a queue rather than running them while the user waits; then, the worker processes work their way through the queued backlog

Most websites will need background jobs like this.


## Basic architecture for background jobs


Sidekiq vs Resque


There many background job tools out there, but two are dominant:
  - Resque
  - Sidekiq

They're both very popular but for different reasons:


### Resque

resque was developed by a little company called GitHub
they use it for background jobs like generating charts
they made it because they found other systems unreliable
they pioneered the use of Redis as a simple queuing system
Redis is very fast key-value store (a DB that's like a ruby hash)
A large ecosystem of 3rd party add-ons are available


### Sidekiq

sidekiq was made by Mike Perham
it was born out of frustration with resque's performance
sidekiq is multi-threaded, while resque is single-threaded
sidekiq does more work with fewer servers (saves $$$)
somewhat compatible with resque tools
growing ecosystem
the industry is moving towards sidekiq (money talks...)

And we're going to use it today.

---

Let's start building a blog app and see how we could use sidekiq to do some slow processing for us.

Let's scaffold it:

    $ rails new sidekiq_app -d postgresql 
    $ cd sidekiq_app 
    $ rails g scaffold post title:string content:text

Then, we can fix the "database.yml" file, delete "index.html" from public, and add a root handler to "routes.rb":

    root to: 'posts#index'

We can now run our migration:

    $ rake db:create
    $ rake db:migrate


Then, we need to add the sidekiq gem to our app's Gemfile, and sinatra as sidekiq uses it for it's dashboard, and a 3rd party gem that enhances the dashboard:
=> see https://github.com/mperham/sidekiq/wiki/Monitoring for more details.

In "Gemfile":

    gem 'sinatra', '>= 1.3.0', require: nil 
    gem 'sidekiq' 
    gem 'sidekiq-failures'

Sidekiq installs a new binary, so we need to rehash after we bundle:

    $ bundle install

We need to let "rbenv" know about the new binary:

    $ rbenv rehash

...and let "zsh" know about the new binary so it stops trying to autocorrect:

    $ hash -rf

- - -

We also need to install and startup Redis:

    $ brew install redis
    $ redis-server /usr/local/etc/redis.conf

- - -

We can now access a few redis commands in terminal, such as the redis command line interface:

    $ redis-cli
    redis 127.0.0.1:6379> GET foo
    (nil)
    redis 127.0.0.1:6379> SET foo 99
    OK
    redis 127.0.0.1:6379> GET foo
    "99"
    redis 127.0.0.1:6379> DEL foo
    (integer) 1
    redis 127.0.0.1:6379> exit

=> it's just a big hash acting as a server.


## Don't keep the customer waiting...


Let's suppose that we want to do some slow processing on the text of any new post submitted to our blog.

We can just use Ruby's `sleep` method to simulate this for now. 

Go to the "PostsController" and add a call to sleep in the "create" function:

    def create 
      @post = Post.new(params[:post]) 
      sleep 10 # sleeping for 10 seconds to represent lots of 'work' 
      # rest of code here # 
    end

Now we can restart the rails server and create a new post. 

Notice that it takes 10 seconds for the browser to respond after clicking submit. This is a demonstration of why we need background jobs.


## Our first worker

When we use sidekiq to run our slow code asynchronously, we do so by moving the slow code into its own class - a worker class. 

We put these in their own folder by convention.

- - -

Let's create a contrived slow process that we can play with. 

To that effect, let's start by creating a "workers" folder in "app". We can put all our workers in this folder.

`/app/workers/demo_worker.rb`

    class DemoWorker 
      include Sidekiq::Worker 
      def perform(delay, message)
        sleep delay # sleeping for a period of seconds to represent lots of 'work' 
        logger.info message 
      end 
    end


We're going to need tabs, lots of tabs


Sidekiq is a separate server that we have to launch from a separate terminal window. 

So press "cmd+t" a couple of times to open a new terminal tab in the project folder.

In the first tab fire up a rails server:

    $ rails s

In the next, fire up rails console:

    $ rails c

We'll use the last tab to run the sidekiq server, but we'll launch that in a moment. 

What we're going to do is:
  - run a job in the console
  - then send the job to sidekiq via redis
  - then send lots of jobs to sidekiq
  - then make our rails app send jobs to sidekiq


## Test a worker locally (synchronous)

We can run the following in the rails console:
    
    w = DemoWorker.new 
    w.perform 5, "testing my DemoWorker" 
    => "testing my DemoWorker" # appears after 5 seconds

We just created an instance of our new DemoWorker class and then ran its perform method.


## Run a worker via sidekiq (asynchronously)

Now let's see how we run our worker as a background job. 

Run the following in the console:

    DemoWorker.perform_async 5, "testing my worker asnychronouly"

We should see a uid returned, such as:

    INFO: Sidekiq client with redis options {}
    => "f7381f485fb1b68d87441891"

What we've just done is call a class method that Sidekiq has given us. 

This method doesn't actually call the worker's perform method, but instead records details of what we want to run in redis, the database that we installed. 

The method call returns the uid of the job we just created, i.e. its id in the redis database.

- - -

When we fire up the sidekiq server, it will regularly check for jobs in redis and perform any that it finds.

In "routes.rb", let's add:

    mount Sidekiq::Web => '/sidekiq'

To confirm this, we can take a look at Sidekiq's dashboard. In our browser, we can visit http://localhost:3000/sidekiq.

We should see Sidekiq's dashboard. This mini sinatra app reads the redis database and tells us what jobs are waiting to be processed.

Take a look at the "Queues" tab. We should see the "default" queue listed, and it should have 1 job enqueued. 

Click on it to see the details. The job we just enqueued (i.e. by calling "DemoWorker.perform_async") should be there. 

This information is being read directly from redis.

As we can see, we have all the information we need to run the job here:
  - the worker class
  - the arguments for the job

If we turn on "Live Poll" the display will be regularly refreshed automatically.

- - -

Now we'll fire up the sidekiq server and we'll see this job disappear.

In the 3rd console tab run:

    $ sidekiq

We'll see some start-up messages, and then messages about starting the job in question. 

Five seconds later we'll see the another message saying that the job has been completed. 

We'll also see the job disappear from the dashboard queue, and the number of processed jobs increase by 1.

Sidekiq is running our full rails stack, and so it has access to all the classes our rails app does. 

When it runs a job, it simply creates a new instance of the appropriate worker class, and then calls it's perform method, passing in the arguments stored in redis.


## Run multiple workers

Now let's see how sidekiq handles a bit more load...

First, switch to the "Dashboard" tab in the sidekiq web interface. This view live polls by default (notice the pulsing red light).

In rails console run the launch 500 async jobs in quick succession:

    500.times { |n| DemoWorker.perform_async 5, "finished job #{n+1} of 500" }

Now take a look at the dashboard: we'll see lots of activity while sidekiq runs all those jobs at once.

We also notice that the dashboard says "25 busy", and that the enqueued number steps down in increments of 25.

Also, take a look at the terminal tab running sidekiq. Notice that a bunch of jobs finish at nearly the same time, and then another bunch get started.

Now take a look at the "Workers" tab in the sidekiq interface. We should see a list of 25 workers. This is because, by default, when we start sidekiq, we actually start 25 workers.

Let's consider what happened when we enqueued these 500 jobs:
  - the 25 workers all take a job (leaving 475 enqueued)
  - they start the job straight away
  - 5 seconds later they all finish at roughly the same time
  - then they all fetch another job (leaving 450)
  - and so on and so on until the queue is empty

This means that it takes roughly 500/25*5 = 100 seconds to complete all the jobs.


## Enqueuing jobs from our rails app

We don't usually drive sidekiq from the console, we're just doing this to understand how it works. 

Now let's use it as intended.

- - -

We start by changing our "PostsController" code so that it now calls our DemoWorker:

def create 
  @post = Post.new(params[:post]) 
  DemoWorker.perform_async 10, "just finished some slow processing in the PostsController..." 
  # rest of code here... # 
end

Back in Chrome, create a new post and now we are responding to the user's request almost instantaneously, whilst at the same time triggering our slow processing.


## Retries

What if the code our worker runs goes wrong for some reason? 

Sidekiq has a policy of retrying the code multiple times, leaving more and more time between successive attempts, until it finally gives up.

To demonstrate this, let's enqueue a job with the wrong number of arguments. 

From the console (restart if necessary):

    DemoWorker.perform_async "do lots of work please"

Check out the sidekiq web interface. We should see that the failed count has increased by 1, and that we have 1 retry pending. 

Visit the "Retries" tab for more details. 

We can cancel the retry attempts here if we want to.


## Failures tab


Check out the terminal tab running sidekiq. 

Notice that we are getting an error message printed periodically as sidekiq retries the job.

This info is very useful, but by default the sidekiq web interface doesn't give us access to this info. 

That's why we included the "sidekiq-failures" gem. 

It gives us a "Failures" tab that provides more info about the jobs that have gone wrong.

It also lets us reset the failure count - which is handy.


## Preventing retries


To tell sidekiq not to retry our jobs, we can add a line to our worker.

In "demo_worker.rb", add:

    sidekiq_options retry: false

...or to limit the number of retries:

    sidekiq_options retry: 5


## Scheduled jobs


We don't always want to perform a background job straight away. 

For instance, we might want to send an email to a user 24 hours after they sign up to your site, encouraging them to play with your app a bit more.

See https://github.com/mperham/sidekiq/wiki/Scheduled-Jobs for more details.

- - -

Sidekiq gives us another method that lets us do this. 

In the rails console try:

    DemoWorker.perform_in 30.seconds, 0, "scheduled for 30 secs from #{Time.now}"

Take a look at the "Scheduled" tab. It now shows your job. 

Turn on live polling and it should disappear in roughly 30 seconds. "Roughly", because sidekiq only checks redis for new jobs every 15 seconds by default, which means that the job might be run after 45 seconds.

When sidekiq finds a job that is due to be run, it enqueues it so that a worker can pick it up. 

If there are already lots of jobs in the queue, then this can add further delays. 

We can confirm this by scheduling lots of slow jobs:

    100.times { |n| DemoWorker.perform_in 10.seconds, 5, "finished job #{n+1} of 500" }

For these reasons, sidekiq is clearly not a way to schedule jobs with pinpoint accuracy, but we rarely need this.

If we have a particular time we want to perform a job we can use "perform_at" (as opposed to "perform_in"):

    dinner_time = Time.parse "19:00" 
    DemoWorker.perform_at dinner_time, 0, "your dinner's ready!"

If the scheduled time is in the past, the job runs immediately.


## Multiple queues and priorities

We just saw how jobs have to wait their turn with sidekiq. But what if some jobs are more important than others, and need to be prioritised?

Sidekiq supports the concept of multiple queues. 

By default, jobs are added to the default queue as we've seen. 

We can specify a different queue for a worker by adding a line:
sidekiq_options queue: :urgent

When we start our sidekiq server, it only checks the default queue by default. 

But we can start sidekiq with some options that tell it what queues to check, and how important they are relative to each other:

    $ bundle exec sidekiq -q urgent,10 -q default,1

(running with `bundle exec` stops other gems conflicting...)

This example says that our urgent queue should be checked 10 times more often than our default queue.

- - -

We can test this with a new worker.

`/app/workers/urgent_worker.rb`

    class UrgentWorker 
      include Sidekiq::Worker 
      sidekiq_options retry: false, queue: :urgent 
      def perform(delay, message) 
        sleep delay 
        logger.info message 
      end 
    end

Restart the console, and restart sidekiq using:

    $ bundle exec sidekiq -q urgent,10 -q default,1

Now run this in the console:

    100.times { |n| DemoWorker.perform_async(10, "finished job #{n} of 100") }; UrgentWorker.perform_async(5, "URGENT JOB FINISHED") 

... and in Chrome, watch the "Queues" tab with live polling. 

Notice that even though we launch all 100 DemoWorker jobs before the UrgentWorker job, the latter gets run immediately after the first 25 workers finish.


## Securing the dashboard


While the sidekiq web interface is very handy, it's a security nightmare as we have it set up at the moment. 

To make sure only admins have access we need something like this in our routes file.

In "config/routes.rb":

    authenticate :user, lambda { |u| u.admin? } do 
      mount Sidekiq::Web => '/sidekiq' 
    end

Devise gives us a suitable authenticate method, so let's add it to our Gemfile and then run:

    $ rails g devise:install 
    $ rails g devise user

Let's add an "admin" column to the generated migration:

    t.boolean :admin

And run the migration:

    $ rake db:migrate

- - -

Now we can add the corresponding login links etc., to our layout:

    <!DOCTYPE html>
    <html>
    <head>
     <title>SidekiqApp</title>
     <%= stylesheet_link_tag    "application", media: "all" %>
     <%= javascript_include_tag "application" %>
     <%= csrf_meta_tags %>
    </head>
    <body>
     
      <div id="user_nav">
       <% if user_signed_in? %>
         Signed in as <%= current_user.email %>. Not you?
         <%= link_to "Sign out", destroy_user_session_path, method: :delete %>
       <% else %>
         <%= link_to "Sign up", new_user_registration_path %> or <%= link_to "sign in", new_user_session_path %>
       <% end %>
      </div>
     
      <%= yield %>
     
    </body>
    </html>

Sign up, and then in console update the new user to be an admin.

We should now only be able to access the dashboard when signed is as this user, or as another admin.


## Deployment


It's easy to start the redis and sidekiq servers up on a machine we have command line access on, but if we use Heroku we don't have this. 

For this reason, we need to add an extra file to our app that tells Heroku that we need to run sidekiq as well as our rails server.

- - -

This is beyond the scope of this lesson, but if you want to try adding background workers to your project, then take a look at:

<https://github.com/mperham/sidekiq/wiki/Deployment>
<https://devcenter.heroku.com/articles/procfile>


###LAB: auto-tagging posts


Add a worker process that asynchronously calls Reuter's open-calais API in order to get the text of each Post analysed. 

This API will return (among other things) a list of tags we can add to the document.

Steps: 
 
- sign up at http://www.opencalais.com/GetStarted
- get an API key  
- visit the github page for https://github.com/PRX/open_calais  
- read the README  
- add the gem to your Gemfile  
- play with this in the console  
- add gem https://github.com/mbleigh/acts-as-taggable-on  
- run the acts-as-taggable-on migration  
- add a worker that adds auto-tags using open-calais
- sit back and marvel at your auto-tagged blog posts!

- - -

Let's start by adding the gem to the gemfile:  
`gem 'open_calais'`    
`gem 'acts-as-taggable-on'`  

Then in terminal:  
`$ bundle install`

Then in the rails console:  
`$ rails c`

```
> open_calais = OpenCalais::Client.new(api_key: 'your api key')
> response = open_calais.enrich('Ruby on Rails is a fantastic web framework. It uses MVC, and the Ruby programming language invented by Matz in Japan.')
> response.tags.map { |t| t[:name] }
```

In terminal:  
`$ rails generate acts_as_taggable_on:migration`  
`$ rake db:migrate`  

- - -

Then in the "post.rb" model, add:

```
class Post < ActiveRecord::Base
  attr_accessible :content, :title, :tag_list
  acts_as_taggable
end
```
- - -

Edit the "_form.html" in "views/posts":

```
<%= form_for(@post) do |f| %>
  <% if @post.errors.any? %>
    <div id="error_explanation">
      <h2><%= pluralize(@post.errors.count, "error") %> prohibited this post from being saved:</h2>

      <ul>
      <% @post.errors.full_messages.each do |msg| %>
        <li><%= msg %></li>
      <% end %>
      </ul>
    </div>
  <% end %>

  <div class="field">
    <%= f.label :title %><br />
    <%= f.text_field :title %>
  </div>
  <div class="field">
    <%= f.label :content %><br />
    <%= f.text_area :content %>
  </div>
  <div class="field">
    <%= f.label :tag_list %><br />
    <%= f.text_field :tag_list %>
  </div>
  <div class="actions">
    <%= f.submit %>
  </div>
<% end %>
```

And in our "views/posts/show.html": 

```
  <p id="notice"><%= notice %></p>
  <p>
    <b>Title:</b>
    <%= @post.title %>
  </p>
  
  <p>
    <b>Content:</b>
    <%= @post.content %>
  </p>
  
  <p>
    <b>Tags:</b>
    <%= @post.tag_list %>
  </p>
  
  <%= link_to 'Edit', edit_post_path(@post) %> |
  <%= link_to 'Back', posts_path %>
```

We can now add tags to our new posts.

- - -

Let's now bring OpenCalais into the mix. 

Since we are using an external API, it might break our application - something to keep in mind when incorporating it. Solution? => we might want to do this asynchronously, by integrating it into a new worker. 

In "app/workers/auto_tag_worker.rb":

```
class AutoTagWorker
  include Sidekiq::Worker
  sidekiq_options retry: 3

  def perform(post_id)
    open_calais = OpenCalais::Client.new(api_key: "bsvvc7mrbrjmmd6vezq4bfdg")
    p = Post.find post_id 
    text = [p.title, p.content].join(" ")
    response = open_calais.enrich text
    tags = response.tags.map { |t| t[:name] }
    p.tag_list.add tags
    p.save!
  end
end
```

All that's left is adding the following code to the "create" and "update" functions of our "posts_controller.rb":  
AutoTagWorker.perform_async @post.id

- - -

We can now restart our server (and make sure that we've got sidekiq running!)

... Behold! When creating a new post, OpenCalais is now generating tags for us.



PROJECT TIME






ADDITIONAL RESOURCES


Delayed jobs with Sideqik:  
<https://github.com/mperham/sidekiq/wiki/Monitoring>  
<https://github.com/mperham/sidekiq/wiki/Scheduled-Jobs>  
<https://github.com/mperham/sidekiq/wiki/Deployment>  
<http://railscasts.com/episodes/366-sidekiq?view=asciicast>  
<https://devcenter.heroku.com/articles/procfile>
<http://www.opencalais.com/GetStarted>
<https://github.com/PRX/open_calais>
<https://github.com/mbleigh/acts-as-taggable-on>