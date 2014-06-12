# De-Demeter

http://www.dan-manges.com/blog/37

git@github.com:Pavling/wdi-dedemeter.git

According to the law of demeter, a model should only talk to its immediate association, don’t talk to the association’s association and association’s property, it is a case of loose coupling.

http://en.wikipedia.org/wiki/Law_of_Demeter

# Bad Smell
    class Invoice < ActiveRecord::Base
      belongs_to :user
    end

    <%= @invoice.user.name %>
    <%= @invoice.user.address.full_address %>
    <%= @invoice.user.phone %>

In this example, invoice model calls the user association's properties :name, :address and :phone, which violates the law of demeter.

And more importantly, breaks horribly if an invoice does not have a user associated (or the user doesn't have an address).

What ways have we seen that can tidy this up (and avoid errors on missing associations)?...

* We could add a 'guard' to make sure the object exists before we call it.

```
<%= invoice.user.name if invoice.user %>
```

* We can use `.try` in the views:

```
<%= @invoice.user.try(:name) %>
```

* We could add some wrapper methods in the model (which also use `.try`).

```
def user_name
  user.try(:name)
end

<%= @invoice.user_name %>
```

# Less smelly

Luckily, rails provides a helper method `delegate` which utilizes the DSL way to generates the wrapper methods. Besides the loose coupling, delegate also prevents the error call method on nil object if you add option :allow_nil => true.


    class Invoice < ActiveRecord::Base
      belongs_to :user
      delegate :name, :phone, to: :user, prefix: true
    end

    <%= @invoice.user_name %>
    <%= @invoice.user_phone %>



# Ruby 'Magic'

Ruby has a method built into the `BasicObject` class called `method_missing` which is invoked whenever the object is sent a message it cannot handle.

The default behaviour of method_missing is to raise the 'Undefined Local Variable or Method' error we're all so familiar with, but because it's *just* a method, we can overload it, and use it to write our own functionality.

It captures two arguments and an optional block; the first argument is the name of the method that was called which was not recognised, the second is a splatted array of all the arguments passed to the unknown method.


    gem 'auto_demeter', :git => 'git://github.com/Pavling/auto_demeter.git'

Spend five minutes looking at the source code of this gem, and try to explain how it works.


