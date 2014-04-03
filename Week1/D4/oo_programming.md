#Object Oriented Programming (OOP)

###Intro to OOP

In functional programming, developers end up having to deal with files containing thousands of methods, several of them depending on one another to function. Needless to say, as the software scales up, it becomes very unmanageable. A lot of languages are dealing only with functional programming. Ruby, on the other hand, offers developers a much friendlier environment to work with: the object-oriented pattern. 

Just like in real life, **everything** in Object-Oriented-Programming (OOP) is an object, and methods are how these objects interact with each other. 

Objects have methods and properties, much like in real life (a pen, for example, will have a "write" method, and an "ink" property). 

In Ruby, we have **classes** (which are also objects). A class is used to specify the form of an object and it combines data representation and methods for manipulating that data into one easy-to-interact-with package. To put it simply: an object is a bunch of methods and properties encapsulated within a class; a class is a block containing some code.

The data and methods within a class are called **members** of the class. Hash, Array, String,... they are all classes. Think of a class as a blueprint from which we create **instances**. The instances will be identical, but separate objects. 

We have different types of methods for our classes (class methods) and for our instances (instance methods).

###Creating your own class

To define a class, we use the keyword "class" followed by the class name "ClassName" (class names that contain more than one word are written using the CamelCase convention). We will close a class with an "end", at the end of the file. 
Example:

Let us define a Person class:

```
$ class Person 
$  def talk(words)
$    puts "I say, #{words}"
$  end
$ end
```

→ we've defined the Person class, and gave it the property "talk".

Then let's instantiate it:  
`$ bob = Person.new`  
→ when creating an instance of a class, we are basically calling the `.new` method on the class. 

Then we can check what type of object Bob is:  
`$ bob.class    RETURNS   Person`

We can call the `.talk()` method which we defined in the Person class. This is an instance method. We are calling it on the instance:  
`$ bob.talk("hello")    RETURNS   "I say, hello"`

Lets reopen our Person class and add a familiar method. Note: we can also reopen Ruby's classes and change them, but this is not advised, as it will confuse other developers working on your code. This is called Monkey-Patching.

Example:

```
$ class Person 
$  def to_s 
$    "My object id is: #{object_id}"
$  end
$ end
```

(We know that the `.to_s` method usually turns the array it is called on into a string. However, as explained above, we are able to reassign it a new action, in this case, to return the ID of the object on which it is called. Again, DON'T do this!)

```
$ bob = Person.new
$ fred = Person.new
$ bob.to_s      RETURNS     My object id is: 226765960
$ fred.to_s   RETURNS   My object id is: 227583904
```

Notice they have different IDs. This illustrates that although they are from the same class, they are separate instances of this class.



###Getters and Setters

####Instance variables:

The instance variables are akin to class attributes, and they become properties of objects once new objects are created using the class. Object attributes are assigned individually and aren't shared with other objects. They are accessed using the @ operator within the class. However, to access them outside of the class, we use "public" methods, which are called **accessor methods**.

####Accessor, setter & getter methods:

To make the variables available outside the class, they must be defined within "accessor" methods. These accessor methods are also known as **getter methods**. 

To illustrate this, lets define a more complex class. It will have an "age" instance variable which we can set (setter) and then read (getter).

Example:

```
$ class Person  
$  def speak 
$    puts "good morning from a #{@age} year old"
$  end
$  def talk(words_to_say)
$    puts "I say, #{words_to_say}"
$  end
$  def age=(value)
$    @age = value
$  end
$  def age
$    @age
$  end
$ end
```

We now have a method to set the age; `age=()` and a method to get the age; `age()`. It's useful to note that there is nothing special about the '=' sign in the setter method, it is purely syntactic sugar as we'll see below.

Now, outside of the class:  

```
$ bob = Person.new
$ bob.speak           RETURNS       good morning from a  year old
$ bob.age   RETURNS   nil
$ bob.age = 21  #equivalent to bob.age=(21)
$ bob.speak           RETURNS     good morning from a 21 year old
$ bob.get_age   RETURNS   21
```

Notice that ruby is being nice to us, letting us access the setter method as if we are using variable assignment `bob.age = 21`. This is exactly equivalent to `bob.age=(21)` which looks more like the method definition.

Fortunately, Ruby gives us a simpler way of achieving this, by using "attr_writer", "attr_reader" and "attr_accessor". We are building an "attr_..." with a **:symbol** (or several, separated by commas, if you want to have access to several elements) - never with a string or a number.

Example:

```
$ Class Person  
$  attr_reader :age
$  attr_writer :age
```

```
$  def speak
$    puts "I say, #{words_to_say}"
$  end
```

`$ end`

Now, outside of the class:

```
$ alice = Person.new
$ alice.age = 18
$ alice.age   RETURNS   18
```


A faster way to set your attribute to be both readable and writable it to use "attr_accessor":

```
class Person  
  attr_accessor :age, :gender

  def talk say_what
    puts "just said '#{say_what}'"
  end

end
```

Now, outside of the class:

```
alice = Person.new
alice.age = 18
alice.age   RETURNS   18
```


###Initializers

When creating an instance of a class, we can also pass arguments to the `.new ` method, and handle these arguments on the class side. That's where the "initialize" method comes in handy.

The "initialize" method is a standard Ruby class method. It is very useful when initializing class variables at the time of the creation of the object. This method may take a list of parameters, and like any other Ruby method, it is preceded by the "def" keyword.

Example:

```
  def initialize first_name, last_name, age
     @first_name = first_name
     @last_name = last_name
     @age = age
  end
```

To illustrate this concept, let's rewrite the Person class with an initializer so we can set the age at the time of the creation of the instance. We do this using an instance hash.

If we take our Person class example from earlier:  

```
class Person  
  def initialize(options={})
    @age = options[:age]
  end
  def get_age
    @age
  end
end 
```

```
fred = Person.new(age: 25)
fred.get_age    RETURNS   25
```


###Class variables

**Class variables** are variables which are shared between all instances of a class. Class variables are prefixed with two @ characters (@@). A class variable must be initialized within the class definition.

So far we have been building instance variables. Let's look at an example of class variable:

```
class Person  
  @@toe_count = 10
  def initialize(options={})
    @age = options[:age]
  end
  def get_age
    @age
  end
  def toe_count
    @@toe_count
  end
  def toe_count=(value)
    @@toe_count = value
  end
end 
```

```
fred = Person.new
wilma = Person.new
fred.toe_count          RETURNS         10
wilma.toe_count         RETURNS         10
fred.toe_count = 9
fred.toe_count          RETURNS         9
wilma.toe_count         RETURNS         9
```

###Class methods

A **class method** is defined using the keyword "def" followed by "self.methodname()". As you can see, to define a class method, we precede our method with "self".  We close it with the "end" delimiter and write its class name as "classname.methodname".

Example:

```
class Person  

  attr_accessor :age
  attr_accessor :parent1
  attr_accessor :parent2

  def initialize(options={})
    @age = options[:age]
    @parent1 = options[:parent1]
    @parent2 = options[:parent2]
  end
```


By using self here, we are defining a class method.

```
  def self.marry(person1, person2)
    self.new: parent1 => person1, parent2 => person2
  end

end 
```

```
bob = Person.new(age: 23)
bob.age = 55
```

The attr_accessor makes "age" and "parents" available to us outside of the Person class.

```
bob.parent1 RETURNS   nil
bob.parent2 RETURNS   nil
Person.marry(Person.new, Person.new)    
RETURNS   Person1 @age=nil, parent1=Person2, parent2=Person3
``` 


###Class Constants

You can define a **constant** inside a class and assign it a direct numeric or string value. It is defined without using @ or @@. By convention, we write the name of class constants in all-caps.

Once a constant is defined, you cannot change its value, but you can access it directly inside a class - much like a variable. However, if you want to access a constant outside of the class, you need to use the `ClassName::CONSTANT` syntax.

```
$ class Hacker
$   LANGUAGE = "Ruby"
$ end
```

```
person = Hacker.new
person::LANGUAGE    RETURNS "Ruby"
```