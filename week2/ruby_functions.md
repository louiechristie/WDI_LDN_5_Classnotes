## Ruby Functions

When you need to reuse code in several places in an application, instead of duplicating the code, you encapsulate it into something called a 'method'. This means we can reuse blocks of code, which is the DRY (Don't Repeat Yourself) way of working. This speeds up production, and makes it easier to refactor and debug  code.

###How to declare a method

You define a method by using the keyword `def` followed by the method_name

```
def method_name
  ... content of the method ...
end
```

Example:

```
$ def say_hello
$ puts "Hello, wdi"
$ end

$ say_hello  
=> "Hello, wdi"
```

Now we can call the method "method_name" anywhere in the script. 

An application of this would be to use a method to abstract the VAT rate

```
def vat_rate
  20
end
```

This way, if the VAT rate changes, we can then change our VAT rate in only one place in our code, instead of changing its numeric value in several places throughout our code.

Remember: a method name can't begin with a number! 


###Arguments

A function can take arguments (or parameters) when called. In order to give parameters to a function, you need to pass them in after the method call:

```
def say_hello(name)  #the parentheses are optional 
  puts "Hello,  #{name}"
end

$ say_hello("Gerry")
=> "Hello, Gerry"
```


Another example:

```
def greet(name)
  puts "hi #{name}"
end
```

So when we call greet we must pass in this parameter  
`greet("Julien")`   RETURNS   `hi Julien`

However if we try to call this method without passing the parameter, it will break. We can fix this by giving name a default value:  

```
def greet(name="you")
  puts "hi #{name}"
end
```

`greet("Julien")`   RETURNS   `hi Julien`
`greet`       RETURNS   `hi you`


You can pass several arguments to a method. Arguments can be of any type:  

```
def say_hello(what_to_say, names)
   puts "Hello  #{names.join(",")}"
end

$ say_hello(["Gerry", "Jon"])  
=> "Hello Gerry, Jon"
```

If we have an unknown amount of parameters we can use a splat:  

```
def greet(*names)
  puts "hi #{names}"
end
```

`greet("Jon", "David", "Julien")` RETURNS `hi ['Jon', 'David', 'Julien']`
`greet("Jon", "David")`     RETURNS   `hi ['Jon', 'David']`

We can pass a splat parameter alongside our parameters with default values:  

```
def greet(name="you", *names)
  puts "hi #{name} and #{names}"
end
```

`greet("Jon", "David", "Julien")` RETURNS `hi Jon and ['David', 'Julien']`


###Return value

By default, ruby returns the last expressed value, which means you usually won't need to explicitly return something. When you're in the console, the return value is always displayed after a call.

###! and ? methods

There is a convention in the ruby world: when a method is actually changing the object where the method itself has been implemented , the method's name ends with `!`

When the method returns a boolean value (true or false), then the method name should end by `?`