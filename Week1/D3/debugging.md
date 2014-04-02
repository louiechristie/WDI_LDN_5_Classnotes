## RUBY Debugging

### Debugging with Pry

First install "pry" and "pry-byebug":  
`$ gem install pry pry-byebug`   
(Remember, you can chain several gem names after the install command)

After you've installed the gem, don't forget to do:  
`$ rbenv rehash` 

Pry is a replacement for "irb". It basically adds all kinds of nice features, including features that will help you quite a lot when debugging code.

Let's give it a try:  
`$ pry`  
`> 2 + 2`  
`=> 4`  

To exit from "pry", type `exit`

Pry makes output a bit prettier than "irb", by using colour, and by laying things out better.

One of pry's best features is its ability to freeze the execution of a file of ruby commands, letting you take a peek inside the running program.

One way of understanding how the new code works is to step through it one line at a time.

First of all, we have to "require" a gem somewhere in our code in order to use it in our program, so we need to add a "require..." statement for each of the new gems (usually added at the very top of your file):

So, opening you file in sublime (`$ subl buggy_calc.rb`), add:   
  `require 'pry'`  
  `require 'pry-byebug'`

Now we can put a  `binding.pry`  call in our code â†’ it is our breakpoint. Our code will stop here when we run it, allowing us to step through the rest of the code one line at a time in the console.

Run the program. 

We end up into pry's debugging environment, where we get a snippet of code showing where we are. We now have a normal interactive ruby environment. 

Test this by typing:  
`> 2 + 2`  
`=> 4`

In this environment, you also have access to the variables present in the running program. You can access these by typing their name, or by using a new method that pry has added to the environment:  
`> ls`

This shows all our local variables. We didn't create them all, some were created automatically, like `\_file\_`.

To see a list of all the local variables (we created) add an  `-l`  flag (for local)  
`> ls -l`

From this point forward, we can see the state of our program's variables (i.e. "operation", "a" and "b").

We can step through our code by typing  `step`  or just  `s` .  
â†’ this moves us to the next line, and redraws the code snippet.

*NOTE*: if your terminal window is too small for the snippet to display entirely, you can scroll. You will need to press "q" to exit from this scrolling.

If we keep typing step, we will be taken into the case statement, and then we jump to the method for the operation we called (e.g. `add()` ).

To continue running the program without debugging, we can type `continue`, or just  `c`.

*NOTE*: if we run the loop again, we hit `binding.pry` again, and the execution of the program will pause again. Just delete the `binding.pry` line to stop this process (and yes, as always, remember to **SAVE** your file after you've made changes to it!).


###More features, more problems

Let's try another example. 

We want to add some validation in our program that will warn the user if they entered a string instead of a number.

To that effect, we replace the `gets.to_f` calls with a `get_number` method.  

â†’ this is intended to do the gets as well as the number conversion, but it will warn the user if the string doesn't contain a number.

Once you've replace all the `gets.to_f` with `get_number`, re-run your file. 

You're getting an error message? Let's put a `binding.pry` in the `add` function.

It would seem that we aren't getting the numbers we are expecting; `get_number` must have a problem.

However, even though the bug is presumably in `get_number`, you can see that this method didn't cause an error. This is common in a "dynamically typed" programming language like ruby â†’ you have a function that isn't returning without an error, but isn't returning the type of value you expected.

By putting a `binding.pry` in `get_number`, we can now step through the code. We are now able to see how number checking works, and it all seems to work as expected. However, when we are back in basic_calc, and look at what `ls -l` gives us, we can see it returned "nil". The number isn't the last thing in the method. The `unless` clause returns a nil when not triggered, and even when it is triggered, the `puts` it contains also returns nil.

Now that we know where the issue is, we can fix it by adding `output` at the end.

We have successfully debugged our program!


###Other tips

We can prevent pry from going into methods by using `next` rather than `step`. For instance, I can just step over the `get_number` call. It still gets executed (as evidenced by the prompt for input), but the debugger doesn't enter it.

While going through your code step by step, you might tunnel down into the code of a gem that's embedded in your program. If you want out, type `finish` (or `f`), you'll exit the gem's code and get back to yours.

If you got lost and cannot remember where you are in your program, `whereami` will draw the code snippet again.

"Pry" and "pry-byebug" have a lot more features (especially pry) than we've covered in class today. 

Check out [github/pry](https://github.com/pry/pry) for additional information.
