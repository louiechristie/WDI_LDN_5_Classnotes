# Filters + callbacks

## private/public methods
By default, all methods in Ruby classes are public - accessible by anyone.

If desired, the visibility for methods can be restricted by public, private, protected object methods.

Essentially this means whether they can be called on an object.

"private" methods can only be accessed by the instance itself (though it cannot call "self.some_private_method" - it has to call the local scope "some_private_method")

"public" can be called by anyone.

"protected" we'll skip. If you want to know ask me later, but it's rarely used.

As with everything in Ruby - it's not hard-and-fast. And it's possible to access private methods - the point is to indicate that maybe the coder didn't intend for us to.

- Beware of hanging "private" calls
  - Every method after a "private" is private until "public" (or protected) is called.

  I would recommend putting all your private methods at the bottom of the class - additionally, define "private" in front of each, to be explicit, and prevent hiccups when refactoring.

## Filters

- setup app to play in:
  
`rails new facebook_app`  
`cd facebook_app`  
`rails g scaffold User name dob:date`  
`rails g controller sessions new create destroy`  

- routes:  
`resources :users`  
`get '/login', to: 'sessions#new', as: :new_login`  
`get '/logout', to: 'sessions#destroy', as: :logout`  
`get '/fake', to: 'sessions#create', as: :fake_login`  


- Filters are methods that are run before, after or “around” a controller action
  
- Login example

```
class ApplicationController < ActionController::Base
  before_filter :require_login
 
  def current_user
    if current_user_id = session[:current_user_id]
      User.find(current_user_id)
    end
  end

  private
  def require_login
    unless logged_in?
      flash[:error] = "You must be logged in to access this section"
      redirect_to new_login_url # halts request cycle
    end
  end
 
  def logged_in?
    !!current_user
  end

end
```

- Problem is, not *every* action needs the filter... otherwise how would we login in the first place?

```
class LoginsController < ApplicationController
  skip_before_filter :require_login, only: [:new, :create]
end
```

- example of filter use:

```
class ApplicationController < ActionController::Base
  before_filter :authenticate
  around_filter :catch_exceptions
end
```

```
class WeblogController < ApplicationController
  # Will run the :authenticate and :catch_exceptions filters.
end
```

```
class SignupController < ApplicationController
  # Skip :authenticate, run :catch_exceptions.
  skip_before_filter :authenticate
end
```

```
class ProjectsController < ApplicationController
  # Skip :catch_exceptions, run :authenticate.
  skip_filter :catch_exceptions
end
```

```
class ClientsController < ApplicationController
  # Skip :catch_exceptions and :authenticate unless action is index.
  skip_filter :catch_exceptions, :authenticate, :except => :index
end
```

- around_filters

```
 private
 def catch_exceptions
   yield
 rescue => exception
   logger.debug "Caught exception! #{exception}"
   raise
 end
```


## Callbacks
  Like filters, but for ActiveRecord models. They're methods that get called at certain times in an object's lifecycle.

- Callbacks are registed in the class, and generally are defined like this

```
  before_validation :ensure_login_has_a_value
  before_save :normalize_card_number, :if => :paid_with_card?
```

- but can take a block


```
class User < ActiveRecord::Base
  after_initialize do |user|
    puts "You have initialized a User object!"
  end
end
```

- or can be individual methods:

```
def before_save
  puts 'before save'
end

def after_save
  puts 'after_save'
end

def around_save
  puts 'in around save'
  yield
  puts 'out around save'
end
```

- There are callbacks called when creating, updating and destroying objects:

  - Creating an Object  
    before_validation  
    after_validation  
    before_save  
    around_save  
    before_create  
    around_create  
    after_create  
    after_save  

  - Updating an Object  
    before_validation  
    after_validation  
    before_save  
    around_save  
    before_update  
    around_update  
    after_update  
    after_save  
  
  - Destroying an Object  
    before_destroy  
    around_destroy  
    after_destroy  