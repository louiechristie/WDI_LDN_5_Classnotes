#Authorization with CanCan

CanCan is an authorization library for Ruby on Rails which restricts what resources a given user is allowed to access. All permissions are defined in a single location (the Ability class) and not duplicated across controllers, views, and database queries.

We will first need to add the cancan gem to our Gemfile. 

`gem 'cancan'`

And remember to bundle.

Again we will need to create a migration which will add the column role to our users table.

   ` rails g migration AddRoleToUsers`

migrate/TIMESTAMP_add_role_to_users

    class AddRoleToUsers < ActiveRecord::Migration
      def change
        add_column :users, :role, :string
      end
    end

Now run your migration.

    rake db:migrate

And update your user model to include:

    def role?(role)
      self.role.to_s == role.to_s
    end

This time we will use cancan to generate our abilities. 

    rails g cancan:ability 

Now in the generated file add:

    class Ability
      include CanCan::Ability
     
      def initialize(user)
        user ||= User.new
        if user.role? :admin
          can :manage, :all
        else
          can :read, User
        end
      end
    end

The `can` method has lots of options, read about them here: https://github.com/ryanb/cancan/wiki/Defining-Abilities.

We can check abilities in controllers and views with `can?` and `cannot?`:

For individual records:

    can? :destroy, @recipe

Or class:

    <% if can? :create, Recipe %>
      <%= link_to "New Recipe", new_recipe_path %>
    <% end %>


In controller actions we can authorize with `authorize!`:

    authorize! :show, @recipe



Or we can use a shortcut helper, which will both load the controller's resource (@recipe in the RecipesController for instance) and authorize it for every action:

    load_and_authorize_resource

Now if we try and access a secure page using the user role, we will be displayed a CanCan error page. We can redirect from this error page, and add an alert message, by updating our ApplicationController to include this method:

    rescue_from CanCan::AccessDenied do |exception|
     redirect_to root_url , alert: "You can't access this page"
    end
