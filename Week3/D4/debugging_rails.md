# Debugging Rails

Debugging rails apps used to be very tricky, but thankfully there are now a number of tools that make it much easier. 

Before we look at rails-specific methods, let’s take a look at a method that we can use with many different programs.


##Log Files


###Log tailing

Most long-running applications (e.g. web servers, database servers) write messages in a log file to let us know what's going on.

When we start up our rails server using `$ rails s`, it records log messages in:   
`$ <RAILS_ROOT>/log/development.log`  
...because the default "environment" is "development".

If we start the server in the production environment using `$ rails s -e production`, then it records log messages in:  
`<RAILS_ROOT>/log/production.log`

These files can get very long over time - therefore, it isn't really a good idea to open them in a text editor like "sublime". 

We usually only need to see the last few lines of a log file anyway, as this is were the most recent information can be found.

A very useful command for working with log files is `tail`. This method just shows the last part of the file.

###Follow mode

Constantly entering `tail` commands every time we want to see the end of a log file can be tiresome. Fortunately, the `tail` method has a convenient "follow" mode that will track changes in the tailed file and update the display. 

To use this mode, we simply need to add the `-f ` flag to our call, e.g.:  
`$ tail -f log/development.log`

We don't usually need to do this when developing, as the terminal tab that is running the server usually shows us the same thing. However, this can be useful when we need to examine the log on a production server, as we don't usually have access to server output.

###Log Level

It is common to be able to set a "log level" (or "verbosity level") when configuring a server. 

This tells the server how much information we want it to write in the log file. When a server has a high log level set, it will only write messages in the log that are deemed "important". When it has a low level set it will record every little detail of what it's doing.

###Rails Log Level

In rails, the available log levels are (in order of verbosity):  

* debug - lots of logging
* info
* warn
* error
* fatal - virtually no logging

The default log level for the development and test environments is "debug", as we usually want lots of information when developing or testing our code. 

However, the default level for production is "info", as we don't usually want lots of detail in this mode.
We can change the log level by adding a config setting in the relevant environment file. 

For instance we could add:
`$ config.log_level = :error` at the end of "config/environments/development.rb" and our development log file would (after a server restart) become considerably less chatty. 

This config setting can be useful when we encounter a problem on the production server that we can't recreate in development. 

If we restart our production servers with debug level logging, we can get more insight into what the problem might be.

###Writing to the Rails Log

We can also add our own messages to the log using `logger` from within a controller or model. 

To that effect, we can just append a method call corresponding to the log level we want our message to have, e.g.:  
`logger.debug "the values of @planet's attributes are #{@planet.attributes.inspect}"`  
  `logger.info "creating a new planet…"`

###Postgres Log Level

The postgres database server's files should be located in: "/usr/local/var/postgres". 

It is in this folder that we will find "server.log", which is the default file that postgres logs to. We can tails this with:  
`$ tail -f /usr/local/var/postgres/server.log`

Looking at our Planets app, if we click on a few links on different pages, we won't see any changes to the log. This is because, by default, postgres has a really high log level. To change this we need to change a config setting and restart the server.

Open the config file with:  
`$ subl /usr/local/var/postgres/postgresql.conf `

As the file can be long and different for everyone, we can use "Cmd+F" to find the line that has `log_statement`. Uncomment this and set it to 'all' like this:  
`log_statement = 'all'`

We now need to restart the server for this change to take effect. We won't be able to restart it if there are any active connections. We thus need to shut down any rails or sinatra servers that are currently running. Then we can enter:  
`$ pg_ctl -D /usr/local/var/postgres -l /usr/local/var/postgres/server.log restart `

Now we just start following the postgres log file again, using the following command:  
`$ tail -f /usr/local/var/postgres/server.log`

To see those changes take effect, we can restart the Planets app and click through a few links. We should now see that the log file is recording in great detail every SQL transaction. 

This can be very useful when trying to debug a tricky DB problem.
To restore the log level, we can undo the change to the config file, shut down the rails app again, and then restart the postgres server.


##Debug - Better errors & Rails panel


###Debug Helper

If we want to display the debug information about an object in our views, we can use a rails view helper method called `debug`. Using this is similar to putting `puts` statements in a command-line app. Let’s add a debug call at the top of the "app/views/planets/show.html.erb" template:  
`<%= debug @planet %>`

When we visit a planet's "show" page, we now see diagnostic information, e.g.: 

``` 
--- !ruby/object:Planet attributes: id: 2 name: Mecury image: http://upload.widavidedia.org/wikipedia/commons/thumb/3/3f/MercuryGlobe-MESSENGERmosaiccenteredat0degN-0degE.jpg/540px-MercuryGlobe-MESSENGERmosaiccenteredat0degN-0degE.jpg orbit: 0.387 diameter: 0.3829 mass: 0.055 moons: 0 planettype: rocky rings: false createdat: 2013-10-16 02:01:34.042140000 Z updated_at: 2013-10-16 02:01:34.042140000 Z
```

###Pry:

We've already seen "pry" and "pry-byebug", and we can use these in our rails app just like we can in any other ruby code we write. 

This means we can debug using `binding.pry` calls in any of our rails app code.

One minor problem is that, by default, the `$ rails c` command launches "irb". To replace the rails console with pry, we simply need to install the "pry-rails" gem instead.

Edit the Gemfile to include these gems in our development environment:

```
group :development do 
  gem 'pry-rails' 
  gem 'pry-byebug' 
end
```

Pro-tip: remember to run `$ bundle install` and restart your rails server every time we’ve added a gem to a rails project.

###Better errors:

By default, Rails already gives us pretty helpful error messages in the browser when there's a problem with our code. 

However, a new gem called "better_errors" can give us, well, even better error messages, and a way to immediately diagnose the problem.

In short, "better_errors" replaces the standard Rails error page with a much better and more useful error page.

Features:

* Full stack trace
* Source code inspection for all stack frames (with highlighting)
* Local and instance variable inspection
* Live REPL on every stack frame

Example:

![better erros example](https://lh5.googleusercontent.com/yOmgdvqG6SgmH8gbzINp9VNoJ3jgn5cRpilD8t-vlLNDHyCueghCR6-02fbdzY65MVSYFLaGAew1_yD2aRL3uwMtKCkqpfZeHHyhFoHZCd1ELaGqODHdmJSW2w)

As we can see, we are displayed a very clear error form. It also provides a REPL at the point at which the code broke. 

It is important to remember NOT to run "better_errors" in production. Instead, we need to ensure that we are putting it in the development block of our gemfile.

We can add the following gems to the development group:  
`gem 'better_errors' `  
`gem 'binding_of_caller'`  

Again, we need to remember to run `$ bundle install` and to restart the server.

###Playing with "better_errors":

Now we can try this out, and add some code that'll break our app so that we can see how "better_errors" works.

Go into the "planets_controller.rb" file, and to the top of the update method, add a call to a method `do_something_stupid`.

At the bottom of the controller, we can add the following method definition:

```
def do_something_stupid 
  x = 100 
  y = 0 
  x / y 
end 
```

Now, let’s visit the edit link for a planet and click submit. We can see that we now get a very different looking error page. 

Amazingly, "better_errors" gives us a REPL that is paused at the point where the error occurred. 

It is acting very similarly to our previous "binding.pry" approach, with the added advantage of placing that REPL in the right spot to catch the error. 

When playing with the REPL, we can examine the value of variables in the current context.

We can also see lots of details about the web request, and current variable values displayed below.

On the left we can see the current call stack. Clicking on another "frame" moves the REPL to the relevant line and lets us examine the state of variables in that part of the code.

If we click on "all frames", we can actually examine the state of the program in the gems we are using (i.e. we can debug code that we didn't even write!).

This, as we’ll see more and more, is an incredibly powerful way of debugging rails.

###Rails Panel:

Last, but not least: there is a very convenient extension we can add to Chrome, called "RailsPanel". We can install it by visiting the [Chrome store](https://chrome.google.com/webstore/detail/railspanel/gjpfobpafnhjhbajcjgccbbdofdckggg) and searching for it.

More specifically, "RailsPanel" is a Chrome extension for Rails development that will end our tailing of development.log. 

It allows us to have all the relevant information about our Rails app requests in the browser conveniently bundled in one place: in the Developer Tools panel. 

It also provides insight into the database/rendering/total times, parameter list, rendered views and more.

![rails panel](https://lh3.googleusercontent.com/yYgjIQsiG8RiVBLnaWx070O7Z9smQ4FgV-OefgLWEevL642G6ju5Uozf_57VUW3bUxib41kZqwgYTYPXS7mcmgqIf1GJNUwFyr1-ea3zlMb_onp-jrUeM0WcqA)

However, this extension will only work if our application has a gem already installed called "meta_request", so we also need to add it to the development group in our Gemfile:

```
group :development do
  gem 'meta_request'
end
```

… and run the `$ bundle install` command again.


We can now open the "developer tools" console in Chrome. 

If we restart our Planets app and start clicking on links, we can see that the web requests are listed in a new Rails Panel tab. This tab displays information about how long various parts of the app took to handle the request. 

We can also click on tabs to the right to see the SQL that is being run for a given request.

We also have the option to further customize the gem. For example, if we want references to files to open in Sublime, we can go to "extension options" and select Sublime. 

For this, we'll also need to install an application which can be downloaded here: <https://github.com/exalted/sublimetext-launcher>
 

#ADDITIONAL RESOURCES


Debugging Rails Apps:  
<https://github.com/charliesome/better_errors>  
<https://github.com/dejan/rails_panel>  
<https://chrome.google.com/webstore/detail/railspanel/gjpfobpafnhjhbajcjgccbbdofdckggg>  
<https://github.com/exalted/sublimetext-launcher>
