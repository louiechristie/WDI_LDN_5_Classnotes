#MODULES/MIXINS

Based on what we've observed so far, there must be a better way to organise code than writing a big list of methods in a single file. Earlier this week, we introduced the idea that we can describe an app into a set of object classes, and give each object its own file. 

We can now break our code up by placing a method inside the relevant object definition.

However, if an object can only inherit methods from its parent, then that's quite limiting, as ruby classes can only inherit from one other class.

There are cases where we'll want to include common functionality in classes that don't have a logical common ancestor. 

Example: a building, a business and a person can all have an age. Suppose we have some functions related to calculating an age (using the current date and the "date of birth"), and some related information. 

Where do we put this code? What common class could Building and Person have? "Thing"? That's not very useful. We don't want to put a bunch of methods in a class called Thing.

On the other hand, do we have to just add the duplicate code to each class? That doesn't sound very DRY.

Thankfully, ruby provides us with a way of adding methods to a class via a mechanism other than inheritance. This is where modules come in handy.

Let's look at an example:

Into pry:

Define a module

```
$ module MyModule
$   def my_method
$     "Hello World"
$   end
$ end
```

Define a class

```
$ class MyClass
$   include MyModule
$ end
```

Instantiate the class

`$ my_class = MyClass.new`


Call the modules methods as a class instance method

`$ my_class.my_method`

So you can see how we have "included" the method "my_method" into the class "MyClass". This is called a **mixin**.

While we can only inherit from one parent class, but we can mixin as many module methods as we want.

The whole point of classes and modules is that they allow us to break our code up into more manageable and understandable chunks. 


####Examples:

To help better understand this concept, let's look at a more realistic example of how we would organise a project using modules and classes.

**Mammal App**:

Download the archive file  → "Mammal app".

You can see that it contains a several files. Each file is either:

* a single class definition
* a single Module definition
* or a main file that we actually execute

By convention, we save the files using the lower-case version of the class/module name (it's actually lower-snake-case). So if we created sub-classes of Dog, lets say BigDog and LittleDog, we'd name the files:

* "big_dog.rb"
* "little_dog.rb"

Now, take a look at "main.rb". Notice the `require_relative` calls. As seen previously, this method is responsible for loading the various ruby files into memory. 

The order in which we require the files is important. We must make sure that we have required a file before another file tries to use it. 

We specify the location of the files relative to the current file, but we can omit the ".rb" off the end as ruby knows the code will be ruby.

Because the files are in the same folder as "main.rb", we just need to write the name of the file.

Ruby needs to load the file defining a class or module, before that class or module is mentioned in the program. So for instance, we MUST load "talkable.rb" and "mammal.rb" before "person.rb" and "dog.rb", as the latter two files mention the constants Mammal and Talkable.

Looking at the Mammal app: 

In main.rb

```
require_relative 'talkable'
require_relative 'person'
require_relative 'dog'
```

In talkable.rb

```
module Talkable
  def say(value)
      puts "I say, {#value}"
  end
  def shout(value)
    puts "I say #{value.upcase}"
  end
end
```

In person.rb (in here we need to mixin the modules which are associated with this class)

```
class Person < Mammal
  include Talkable    
end
```

In dog.rb

```
class Dog < Mammal
  include Talkable    
end
```

We can now use these modules through our person class. We have just mixed in in this functionality. This allows us not only to keep our classes small and organised, but also we are able to reuse these mixins in other classes. This keeps our code DRY.

```
$ betty = Person.new
$ betty.say("hello")      RETURNS     "I say hello"
$ ruff = Dog.new
$ ruff.say("bark")      RETURNS     "I say bark"
```

WDI cohort app:

This example is more representative of a real ruby project. Without paying too much attention to the contents and purposes of the code for now, let's try and observe the way the project is structured.

Firstly, notice that the project is organised into folders:

```
"lib" - contains modules that might be used by several classes
"models" - contains classes that model real world objects
"main.rb" - an executable that you run
```

There is no correct way to organise your code, but this structure is quite common. As we'll discover in a few weeks, Ruby on Rails has its own strong conventions when it comes to code organisation.

The basic idea behind this app is to:

* keep track of the people involved in an WDI cohort
* print out a list of all the students and instructors
* order instructors by name and students by grade average
* add an indicator to a person's list entry if it's their birthday today

Now that we have this new folder layout, let's look at the way we require file.

First of all, let's look at the Person class. This class includes the `ActsAsAgeable module`, which is responsible for calculating "age" from "d.o.b." and other age-related elements. This is in a module, because we might want to add these methods to other classes (e.g. a building can have an age, as we mentioned above).

Notice that the `require_relative` call is a bit more complicated than before. 

We need to write a path using normal unix conventions. So the ".." tells ruby to go up a directory, i.e. to the app's root folder ("wdi_cohort_app"), and then go back down to the "lib" folder.


###Mixing in core ruby modules

In the same "wdi_cohort_app" example, we notice the presence of a module called `Comparable`. This is a standard ruby module → we don't need to tell ruby where to find the file, it loads it automatically.

This module is responsible for adding methods that let you compare things.

For example, let's take a look at fred's methods in pry with `$ ls fred`. 

Notice that a few comparator methods are added by the `Comparable` module. 

PRO-TIP: Being able to see where an object gets its methods from is one of pry's best features.

Now, in order for the `Comparable` module to work its magic, we have to fulfil its expectations. It has one expectation. Any class we include it in, must define a `<=> `method, so that it can answer the question: "is this thing greater, less or the same as another thing of the same type?" 

If we don't define it, we'll get an error.

Now let's take a look at the way we have defined the natural sort order for "people". We are simply stating that they should be sorted based on their name, i.e. in alphabetical order.


###Methods mixed in from ActsAsAgeable

Let us keep looking at the "wdi_cohort_app" example.

The custom `to_s` method expects a method or variable "age" to be available. 

When ruby fails to find this in the current file, it is going to look for it in the included modules and the parent class. In this case, it will find "age" defined in the `ActsAsAgeable` module. 

Let's look at that in "acts_as_ageable.rb".

The "age" method is a bit complex - you're welcome to spend a bit of time trying to understand what it is doing. However, let us look at how things work.

Firstly, it expects a variable or method to exist called "dob" (i.e. Date of Birth). Since it isn't defined in this module, this is clearly an expectation of this module. Should we have to write documentation for this module, we would have to make it clear to future users that they have to provide a dob method in the class they mix this module into.

Also, this method references a "now" method that IS defined in the module, but it comes after a keyword "private". 

This tells ruby that this method is only intended to be used by other methods in the module (or a class it is mixed into). This means that we can't call "now" on any of our person objects. We can try and use it, but we will get an error. 

We use "private" so that other developers know that they can't rely on this method in the future iterations of the code. We might refactor the code and not need this any more soon. The flip side of that argument is that other developers should be able to trust that the other methods will be available in the future.


###Calling name-spaced module methods

Let us keep working with our "wdi_cohort_app" example.

Modules have the equivalent of class methods. The "parse_date_string" method is an example. Notice that this method ISN'T mixed into Person. We call directly on the module name. 

This is a handy way of dealing with naming conflicts.

Had we chosen to include another module with a method of the same name, then clearly there'd be no conflict, as they'd each be prefixed by their module name.

The mixed in instance methods like "age" can clash, as these aren't namespaced. The way this type of conflict is resolved is using the ancestor hierarchy. 

Try in pry:
`$ WdiStudent.ancestors`

This returns us a list of all the places this class will look for methods. The ones at the top are the ones it will check first.

Pry also gives you a nice overview of where methods are coming from.


###Redefinitions as super

Take a look at "wdi_student.rb". 

WdiStudent inherits from Person, and so already has `ActsAsAgeable` methods. But we also mix in `ActsAsGradeable`. 

This is a module because other things might be gradeable too, e.g. products. 

Note that the module overrides the `to_s` method that Person defined. This is because we want to display some extra information for students, namely their grade and mean score.

Notice that this method is using `super` so that we retain the functionality of the default `to_s` method while adding to it.

We also override the sort (`<=>`) method, changing the default sort order for wdi-students to descending mean score.


####To sum it up

Modules allow you to create groups of methods, which can then be included into classes. Methods are not like classes, instances cannot be made from them and they cannot be called directly. 
Modules are a great way to organise reusable code and keep things DRY.

Modules/mixins:  
<http://stackoverflow.com/questions/5046831/why-use-rubys-attr-accessor-attr-reader-and-attr-writer>
