#Authorization with Roles

(working on the app we were putting authentication in)

To change permissions depending on user role, we will need to create a migration which will add the column role to our users table.

`rails g migration addRoleToUsers`

migrate/TIMESTAMP_add_role_to_users

```
class addRoleToUsers < ActiveRecord::Migration
  def change
    add_column :users, :role, :string
  end
end
```

Now run your migration.

`rake db:migrate`

Now we will be able to create a new user in irb with a role.
User.create email: "email@email.com", password:"password", password_confirmation: "password", role: "admin"

We will now update our application controller to include can_access_route, and a before filter for it.

application_controller.rb

```
class ApplicationController < ActionController::Base
  helper_method :current_user
  before_filter :can_access_route

  private
  def can_access_route
    raise 'Permissions rejected' unless authorized?(current_user, params[:controller], params[:action])
  end

  private
  def authorized?(user, controller, action)
    case user.try(:role)
      when "admin" then true
      when "user"
        case controller
          when "secret" then false
          when "gossip" then true
        else 
          true
        end
      else
        true
    end
  end

end
```