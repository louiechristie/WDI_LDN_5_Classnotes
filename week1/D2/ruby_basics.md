## Ruby Basics

Ruby is the language that Ruby on Rails is written in. It is the language you will use for all backend code while on this course.

A computer program is simply an instruction, written in a programming language that is interpreted by the computer to perform an action.


###Working with Ruby

**Running Code**

There are two ways of running ruby code:  

* interactively in a shell
* by running a file of ruby code

TIP: Make your code readable and write comments. This will help you and other people to read your code later.

`$ irb`  will open an Interactive Ruby environment in the console (we exit it by typing quit).

'irb' stands for interactive ruby, and typing that into a terminal launches a ruby REPL.  

REPL = Readâ€“evalâ€“print loop â†’ good for experimenting with short snippets of code.

To get into irb, just type `$ irb` - irb allows to quickly try snippets of ruby outside of your application.

We can load files in irb by using  `$ ruby <filename>`  
`$ ruby my_ruby_file.rb` runs a file of ruby code in the terminal.  

What happens? 

* all the instructions in the file are evaluated
* and then you are returned to the shell
* this is how we usually run ruby code


####RubyGems

RubyGems is a package manager for the Ruby programming language that provides a standard format for distributing Ruby programs and libraries. Simply put, it manages the installation of "gems" (ruby libraries).

To see what gems you have installed:  
`$ gem list`

To install a new gem:  
`$ gem install some_gem_name`

Example: 

```  
$ gem install rainbow
Fetching: rainbow-1.1.4.gem (100%)   
=> Successfully installed rainbow-1.1.4
```

Back in irb:
  
```
$ str = "Hello"
=> "Hello"
$ print str.color(:red) â†’ ERROR! We need to "require" the gem we want.
```

To do that:  

```
$ require "rainbow"
=> true
$ print str.color(:red)
hello
```


**Everything in Ruby is an Object**

Ruby is an unusual language in that **everything** is an 'Object'. We can confirm this by asking things what "class" of object they are, using 'irb':  
`> "hello world!".class`  
`=> String`  

`> 99.class`  
`=> Fixnum`  

`> 99.99.class`  
`=> Float`  

We just used "dot notation" to call a "method" on an object.
All ruby objects have the '#class' method - and a whole lot more...

We can find out what type of object we have in Ruby by using `.class`
We can also find out the specific object we have by using `.object_id`


###Ruby Variables

We can store values in "variables" 
 
```
> my_greeting = "hello world!"
> my_greeting.class
=> String
```

Variable in Ruby are mutable, this mean that we can change them. So we can do:  
`> a = 2`  
`> a `    RETURNS   2  
`> a = 5`  
`> a `    RETURNS   5  

It is convention in Ruby that we use **snake case** ie. `first_name` when we are naming variables, and **camel case**, ie. `FirstName` for classes. Finally we have constants. The convention for writing constants is **capital**, ie. `NAME`

There are many different types of variables in Ruby, but for now, we will mainly be using local and instance variables. Very rarely will we use global variables.  

`variable` â†’ local variable  
`@variable` â†’ instance variable  
`@@variable` â†’ class variable  
`$variable `â†’ global variable   
`VARIABLE` â†’ constant  


###Methods

In Ruby objects interact with one another by using what are called methods. More specifically, one object "calls or invokes the methods" of another object. So when we type  `"hello".length`, we are calling the method "length" on the String object.

Remember the "greeting" example?  

```
> my_greeting = "hello world!"
> my_greeting.class
=> String
```

We can also check the class of the object by calling a different method:   

```
> my_greeting.is_a? String or > my_greeting.is_a?(String)
=> true
```

Here, we called the `.is_a?` method, followed by the class name we want to check.

Interestingly, the class of a Class is Class!

```
> String.class
=> Class
```

We can string methods together in Ruby:
`"1".to_i.class`    RETURNS   `Fixnum `  
This will firstly change the string "1" into an integer, then return the Class of Integer.  
Similarly, 
`> my_greeting.is_a?(String).class` RETURNS   `TrueClass`

Some methods we've seen today:  
`to_i` â†’ to integer  
`to_s` â†’ to string  
`to_f` â†’ to float  

There are many, many more methods, and we'll keep discovering and learning new ones over the next few weeks.


###Basic calculus in Ruby

All operators are methods
`+` â†’ plus  
`-` â†’ minus  
`*` â†’ multiply  
`/` â†’ divide  
`=` â†’ equals  
`==` â†’ equal to  
`+=` â†’ adds and returns the new value  
`-=` â†’ substracts and returns the new value   
`!=` â†’ not equal to  
`>` â†’ more than  
`<` â†’ less than  
`>=` â†’ more than or equal to  
`<=` â†’ less than than or equal to  

It is important to remember than when we are using mathematical operators in Ruby, if we are working with Fixnum, we will get a Fixnum returned. For example if we do `5/2` , `2` will be returned. We would do this calculation using floats. So `5.0/2.0 `will return the float value of `2.5`


###Booleans

TIP: here's a helpful link for Ruby â†’ [truth tables](http://memorize.com/ruby-truth-tables/hezwx)     
We only have two states in booleans: "true" or "false".  
`true` is  TrueClass  
`false` is FalseClass  

We also have something called "boolean logic":  
`||` - or  
`&&` - and

So if we type:  
`true || true`    RETURNS   `true`  
â†’ Ruby will only check the first value. As it returns true, it will not bother checking the second value. 

`true && true`    RETURNS     `true`  
â†’ Ruby will check both values to ensure they are both true when using &&.

`nil || false`    RETURNS     `false`  
â†’ In Ruby nil is the same as false.

We can manipulate our booleans to tell Ruby to return a boolean value   
`x = "hello"`  
`is_there_an_x = !!x`  
`x  `     RETURNS     `"hello"`  
`is_ther_an_x ` RETURNS   `true`  


###Strings

If we use the addition operator on strings, we will concatenate them. So `"5" + "5 " `will return `"55"`

We can also use the greater than operator on strings  
`a = "abc"`  
`b = "abd"`  
`a > b`     RETURNS     `false `   
`a < b`     RETURNS     `true`  


####Concatenation

We can join strings together by using concatenation  
`h = "hello"`  
`w = "world"`  
`space = " "`  
`h + space + w`     RETURNS     `"hello world"`  


####Interpolation

We can use interpolation to insert variables into a string (Note: this only works with double quotes, not single)  
`"Hello #{w}"`      RETURNS     `"Hello world"` 

We can get input for our user using gets. As the user will normally press enter when entering a value, we will user chomp to get rid of the extra characters.  
`puts "What is your name"`  
`name = gets.chomp`  
`puts "hi #{name}"`  


###"Advanced" strings

There are various ways to create, and display strings in ruby:  
`$ "hello world"`  
`$ 'hello world'`  

Single quote strings **don't** do interpolation.  
`$ name = "jon"`  

`$ puts "#{name} loves ruby"`  
`jon loves ruby`  
`=> nil`   

`$ puts '#{name} loves ruby'`  
`=> #{name} loves ruby`  
`=> nil`  

Another difference is that double quotes strings let you add special character codes.  

```
$ puts "hello\nworld"
hello
world
=> nil
```

Here, we understand that \n stands for a new line.

So what does a single quote do?

```
$ puts 'hello\nworld'
hello\nworld
=> nil 
```

â†’ it just prints the special character.


####Displaying strings

`p`, `puts` and `print` are different methods in Ruby and they do slightly different things.

`puts` calls `.to_s` on the object and generally returns a readable version of the object.

`p` is equivalent to `puts`, but with the `inspect` method called instead of the `.to_s`. 

`print` calls `to_s` on the object, but it will buffer the stdout unless you set: 
`STDOUT.sync = true`.


####Getting input from the user

```
$ input = gets
=> "hello world\n"
```
  
To get rid of the trailing new line we can use 'chomp'  

```
$ input = gets.chomp
=> "hello world\n"
```

Good thing to know: we can run shell commands from within ruby!  

```
user = 'whoami'
=> "jon\n"

2 = 'whoami'.chomp!
=> "jon"
```

WARNING! NEVER EVER USE THIS COMMAND... but it's good to know about it: we can run ruby code in a string using "eval".  

```
eval "2 + 2"
=> 4
```

Using "eval" with user input is a very bad idea, because malicious users could take advantage of it.



###Program flow

Example: 

```
$ val = 5
$ if val > 4
$ puts "cool"
$ end
cool
=> nil
```

You can also write this on one line:  
`$ if val > 4 then puts "cool" end`  
You only use "then" when writing this on one line. There's no need for it in the three-line version above.

Another example:

```
$ ruby_is_fun = true
$ puts "yay" if ruby_is_fun
yay
=> nil
```


###Conditionals

We will use conditionals regularly in our code to return results according to our conditions. Try to avoid double negatives. If we are not using `else`, and it reads better, we can use `unless` rather than `if`

`if 1 < 2`  
 ` "hello"`  
`end  `       RETURNS   `"hello"`  

We can shorten this by using:  
`"hello" if 1 < 2 `   RETURNS   `"hello"`

Using "unless"  
`"hello" unless 1> 2`   RETURNS   `"hello"`

We can also use "else"       
`if 1 > 2`  
  `"hello"`  
`else`  
  `"bye"`  
`end`         RETURNS   `"bye"`  

Finally, the ternary operator is used to shorten an "if...else" statement.  
`1 < 2 ? "hello" : "bye"  ` RETURNS   `"hello"`


###Case statements

We use case statements to test a variable against specific conditions. 
Example:  

```
$ ruby = "awesome"
=> "awesome"

$ case ruby
$ when "awesome"
$ puts "woot"
$ when "rubbish"
$ puts "booooo"
$ end
```

Another example:

```
$ puts "What was your score"
$ score = gets.chomp.to_i

$ puts case score
$ when 10
$ "Genius"
$ when 5..9
$ "Merit"
$ when 1..5
$ "Not bad"
$ when 0  
$ "Poor"
```

Yet another example:

```
$ x = 4
$ case x
$ when 0..5 then puts "small"
$ when 5..10 then puts "big"
$ else puts "dunno"
$ end
=> "small"
```

Smaller examples:

```
$ if 0 then puts "foo" end
"foo"

$ if false then puts "foo" end
=> nil

$ if nil then puts "foo" end
=> true
```

###Loops

With all loops we need to make sure we break out in some way, otherwise we will have an infinite loop. Which is bad. To get out of an infinite loop, press Ctrl+C

####"For" loop

`$ for i in 1..10`  
`$ print i, " "`  
`$ end  `       RETURNS     `1 2 3 4 5 6 7 8 9 10`  

An alternative to this is:  
`10.times{ |i| print i, " "}` RETURNS     `0 1 2 3 4 5 6 7 8 9`  
or  
`1.upto(10){ |i| print i, " "}` RETURNS     `1 2 3 4 5 6 7 8 9 10`


####"Until" loop

Example:  
`$ x = 0`  
`$ until x == 10`  
`$ puts x += 1`  
`$ end  `       RETURNS     `1 2 3 4 5 6 7 8 9 10`  

Another example:  
`$ until x < 0`  
`$ x -= 1`  
`$ puts x`  
`$ end  `       RETURNS     `1 2 3 4 5 6 7 8 9 10`  

####"While" loop

Example:  
`$ while x < 10`  
`$ puts x`  
`$ end`         RETURNS     `1 2 3 4 5 6 7 8 9`  

Another example:  
`$ x+= 1`  
`$ end while x < 10 ` RETURNS     `1 2 3 4 5 6 7 8 9`  

####"Loop" loop

Example:

```
$ loop do
$ break
$ end 
```

Another example:

```
$ loop do
$ print "type something..."
$ line = gets.chomp
$ break if line =~ /q|Q/
$ end
```


###Last but not least

Interesting observations with `!`

```
!true
=> false

!false
=> true

!nil
=> true
```

