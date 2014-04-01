## LAB: CALC-IT

You have been tasked with creating a basic calculus program. 

This program will run in the command line. It will first prompt the user to choose the operation to execute (add, subtract, multiply, divide). It will then ask for the first number, then the second one.

It should finally return the result of the operation.


Pseudo Code

1. Welcome the user
2. Prompt the user to choose an operation
3. Store the value that the user typed
4. Ask the user for the first number
5. Store that number
6. Ask the user for the second number
7. Store that number
8. Pass the three stored values into a function
9. Display the result


Let's get started! In your working folder, create a ruby file, name it calc_it.rb  
`$ touch calc_it.rb`

Open this file in sublime
`$ subl .`

Once in Sublime:  

```
puts "Hello! Which operation would you like to do?"
puts "(a)dd, (s)ubstract, (m)ultiply, (d)ivide"

operation = gets.chomp.strip.downcase

puts "First number?"
first_number = gets.chomp.to_i

puts "Second number?"
second_number = gets.chomp.to_i
```

##HOMEWORK

###Calc-It extended

Building on the Calc-It app you've done in class today, add the three following features:

####Mortgage Calculator:

Calculate the monthly payment when given the other variables as input.
you need principal, yearly interest rate and the number of payments   
<http://www.wikihow.com/Calculate-Mortgage-Payments>

####BMI Calculator:

Calculate the BMI when given the height and weight - the user should be able to choose between the imperial and the metric system  
<http://en.wikipedia.org/wiki/Body_mass_index>

####Trip Calculator:

This feature asks the user for four inputs:

* Distance how far will you drive?
* MPG what is the fuel efficiency of the car?
* $PG how much does gas cost per gallon?
* Speed how fast will you drive?

The output is a string: "Your trip will take 3.5 hours and cost $255.33."

For every 1 MPH over 60 MPH, reduce the the MPG by 2 MPG (i.e. a car that normally gets 30 mpg would only get 28 mpg if its speed were 61 mph. Yes this gets silly at high speed where mpg goes to zero or gets negative.)


Let's get coding!


###ADDITIONAL RESOURCES

Searching for answers on the web  
<http://www.catb.org/esr/faqs/smart-questions.html>    
<https://www.quora.com/Marketing-Questions-on-Quora/How-can-I-ensure-questions-I-ask-attract-a-large-number-of-followers>

Ruby basics/Ruby functions  
<http://www.tutorialspoint.com/ruby/ruby_quick_guide.htm>  
<http://memorize.com/ruby-truth-tables/hezwx>  
<http://ruby-doc.org/>  

Lab: Calc-It (extended)  
<http://www.wikihow.com/Sample/Mortgage-Payment>  
<http://www.wikihow.com/Calculate-Mortgage-Payments>  
<http://www.wikihow.com/Image:BMI.jpg>  
