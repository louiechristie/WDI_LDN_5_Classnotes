# Rake Tasks

Rake is a build tool. Documentation can be found at: "rational.rdoc". Essentially, rake started as an idea for using Ruby inside automation scripts. 

Create a new empty directory and open it in sublime. Create a new file called RakeFile. This is the file that all rake tasks will require to work. Rake is a gem, we make sure we have it by using:

    gem install rake

We will create a task with the name â€˜make coffeeâ€™:

    task :make_coffee do
     puts "making 2 cups of coffee"
    end

Now if we run:

    rake make_coffee

We will be returned:

    making 2 cups of coffee

We can also add a description to our rake task:

    desc "This is for brewing coffee"
    task :make_coffee do
     puts "making 2 cups of coffee"
    end

We can also pass arguments to our rake task blocks 

    task :make_coffee, :cups do |t,args|
      cups = args[:cups] || 2
      puts "making #{cups} cups of coffee"
    end

Now if we run:

    rake 'make_coffee[4]'

We will be returned:

    making 2 cups of coffee

Sometimes we want to combine many rake tasks into one command. We can do this like:

    task :do_all_the_stuff => [:make_coffee, :switch_of_alarm] do
      puts "ready to go now"
    end


## List of rake commands

We can see all of the available rake commands in the console by using:

    rake -T

Which, when done in a Rais app, gives us some familiar sights, including:

    rake db:migrate - Migrate the database through scripts in db/migrate. Target specific version with VERSION=x
    rake db:schema:dump - Create a db/schema.rb file that can be portably used against any DB supported by AR.
    rake db:schema:load - Load a schema.rb file into the database.

And lots more besides.


## Applying to Rails

    rails new rakeexample

In sublime, we can go into the gemfile and change the database to pgsql:

    bundle

Now we can scaffold event:

    rails g scaffold event title description date:datetime
    rake db:migrate

To make new rake tasks in rails, we have a generator. We can generate a new tasks using:

    rails g task my_namespace create_events

We can update this task to create 10 events for us:

    namespace :my_namespace do 
      desc 'create events randomly'
      task :create_events => :environment do
        10.times do |t|
          Event.create(title: "Event title #{t}", description: "Event description#{t}", date: Time.zone.now+t.days)
        end
      end
    end

Now we can run:

    rake my_namespace:create_events

Now if we go back to our database we should have 10 new events.
