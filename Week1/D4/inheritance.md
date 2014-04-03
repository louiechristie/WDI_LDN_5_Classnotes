## INHERITANCE

One of the most important concepts in object-oriented programming is that of inheritance. Inheritance allows us to define a class in terms of another class, which makes it easier to create and maintain an application.

Inheritance also provides an opportunity to reuse the code functionality and fast implementation time. Unfortunately, Ruby does not support multiple levels of inheritance. However, Ruby supports mixins. A mixin is a specialized implementation of multiple inheritance, in which only the interface portion is inherited. We will talk at length about mixins tomorrow.

When creating a class, instead of writing completely new data members and member functions, the programmer can decide the new class should inherit the members of an existing class. This existing class is called the base class or superclass, and the new class is referred to as the **derived class** or **sub-class**.

The syntax for extending a class is simple. Just add a  < character and the name of the superclass to your class statement.

In short, classes can inherit from other classes. A child class will always inherit from the parent class. 

```
class A
   attr_accessor :random_property
   def self.x
      puts "I am a class method from class A"
   end
   def y
      puts "I am an instance method from class A"
   end
end
```

```
class B < A
end
```

Now in pry:
`$ b = B.new`
`$ b.y  RETURNS "I am a class method from A"`

You can also set the random_property from class A in the child class B.
`$ b.random_property = "I am actually interacting with an attribute in the class A"`

Another example:
A good example is of a mammal. We have different types of mammals, but they all share some common features.

```
class Tetrapod
  attr_accessor :gender
end
```

```
class Mammal < Tetrapod
  def fingers_count
    10
  end
end
```

```
class Dog < Mammal
  def fingers_count
    8
  end
end 
```

```
class Person < Mammal
end
```

```
bob = Person.new
bob.gender = "male"
bob.gender                  RETURNS         "male"
bob.fingers_count             RETURNS           10
ruff = Dog.new
ruff.fingers_count            RETURNS           8
ruff.gender = "female"
ruff.gender                     RETURNS         "female"
```

Even though we do not define "finger_count" in the Person class, it will inherit from the Mammal class. The Dog class also inherits from the Mammal  class, but we have a different "finger_count" in the Dog class. As both Dog and Person inherit from a Mammal, and a Mammal inherits from a Tetrapod, we can access the gender attribute in Mammal, Dog and Person.

####Method Overriding:

Although you can easily new functionality in a derived class, sometimes you would like to modify the behavior of a method that has been defining in the parent class. You can do so simply by keeping the method name and overriding the functionality of the method.

```
class A
   attr_accessor :random_property
   def self.x
      puts "I am a class method from class A"
   end
   def y
      puts "I am an instance method from class A"
   end
end

class B < A
   def y
      puts "I am an instance method from class B"
   end
end
```

Now in pry:
`$ b = B.new`
`$ b.y  RETURNS "I am a class method from B"`

→ As you can see, the "y" method has been overridden.

To get a better idea of the concept of inheritance, you can call the .ancestors method on a Class. It will show the Class' line of inheritance. 

`$ B.ancestors      RETURNS `
`[B, A, Object, PP:ObjectMixin, Kernel, BasicObject]`

##ADDITIONAL RESOURCES

Inheritance:
<http://www.railstips.org/blog/archives/2009/05/11/class-and-instance-methods-in-ruby/>
