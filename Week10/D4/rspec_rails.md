# RSpec on Rails

We're going to integrate RSpec into a Rails app.

    rails new cookbook_the_revenge
    cd cookbook_the_revenge

Edit the gemfile to add the required files.

    group :development, :test do
      gem 'rspec-rails'
      gem 'factory_girl_rails'
    end

    group :test do
      gem 'faker'
      gem 'capybara'
      gem 'guard-rspec'
    end

## Why install in two separate groups? 

rspec-rails and factory_girl_rails are used in both the development and test environments. Specifically, they are used in development by generators weâ€™ll be utilizing shortly. 

The remaining gems are only used when you actually run your specs, so theyâ€™re not necessary to load in development. This also ensures that gems used solely for generating code or running tests arenâ€™t installed in your production environment when you deploy to your server.

Run `bundle` to install.

* rspec-rails includes RSpec itself in a wrapper to make it play nicely with Rails 3.
* factory_girl_rails replaces Railsâ€™ default fixtures for feeding test data to the test suite with much more preferable factories (from ThoughtBot)
* faker generates names, email addresses, and other placeholders for factories.
* capybara makes it easy to programatically simulate your usersâ€™ interactions with your application.
* guard-rspec watches your application and tests and runs specs for you automatically when it detects changes.


## RSpec configuration

    rails g rspec:install

Which creates two files: `.rspec` and `spec/spec_helper.rb`


    # spec/spec_helper.rb
    require "capybara/rspec"

(though weâ€™ll not put this to use until we get into integration testing.)


    # .rspec
    --format documentation

    # config/application.rb
    config.generators do |g|
      g.test_framework :rspec,
        :fixtures => true,
        :view_specs => false,
        :helper_specs => false,
        :routing_specs => false,
        :controller_specs => true,
        :request_specs => true
      g.fixture_replacement :factory_girl, :dir => "spec/factories"
    end

The default `test` folder is no longer needed, so we can delete that (though make sure, if you're working on an old code base, that you're not deleting existing tests)


## Running

Run the command `rake spec` from the application root.

It should say "No examples found", and output that it performed zero examples with zero failures.


## Generate a model

    rails g model Recipe name
    rails g model Ingredient name
    rake db:migrate

The Rails generators create specs and factories for the two models. Edit the two factories to include fake values for their names.

    FactoryGirl.define do
      factory :recipe do
        name { Faker::Lorem.word }
      end
    end

http://rubydoc.info/gems/faker/1.3.0/frames


## Start writing some specs

    it "has a valid factory" do
      expect(FactoryGirl.build(:recipe)).to be_valid
    end

    it "is invalid without a name" do
      expect(FactoryGirl.build(:recipe, name: nil)).to_not be_valid
    end

When we run these specs, they fail... because we haven't added validation to our model to require the presence of a name.

Now it's just a case of writing more tests and code, and learning about Faker, Capybara, Guard, etc. But that's beyond the scope of this lesson.