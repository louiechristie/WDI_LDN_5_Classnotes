# Coffeescript

    brew update
    brew install node

<!--    subl ~/.zshrc
    
    # add /usr/local/share/npm/bin to start of PATH var

-->

    npm install -g coffee-script

    node -v
    npm -v
    coffee -v


http://coffeescript.org/

CoffeeScript is a little language that compiles into JavaScript. Underneath, JavaScript has always had a gorgeous heart. CoffeeScript is an attempt to expose the good parts of JavaScript in a simple way.

The golden rule of CoffeeScript is: "It's just JavaScript". The code compiles one-to-one into the equivalent JS, and there is no interpretation at runtime. You can use any existing JavaScript library seamlessly from CoffeeScript (and vice-versa). The compiled output is readable and pretty-printed, will work in every JavaScript runtime, and tends to run as fast or faster than the equivalent handwritten JavaScript.

It's JavaScript without the fragility...


    alert 'hello world'


# create new file (hello.coffee) and add above code

coffee -p hello.coffee

coffee -c hello.coffee




# more_coffee.coffee

    square = (x) -> x * x

    math =
      root:   Math.sqrt
      square: square
      cube:   (x) -> x * square x

    cubes = (math.cube num for num in [1,2,3,4,5])

    alert cubes



# Splats
'Splat' is the term for the way we handle unknown amounts of parameters in Ruby. JS doesn't support it, but coffeescript enables is.

    gold = silver = rest = "unknown"

    awardMedals = (first, second, others...) ->
      gold   = first
      silver = second
      rest   = others

    contenders = [
      "Michael Phelps"
      "Liu Xiang"
      "Yao Ming"
      "Allyson Felix"
      "Shawn Johnson"
      "Roman Sebrle"
      "Guo Jingjing"
      "Tyson Gay"
      "Asafa Powell"
      "Usain Bolt"
    ]

    awardMedals contenders...

    alert "Gold: " + gold
    alert "Silver: " + silver
    alert "The Field: " + rest




# loops
We replace most loops in coffeescript

    eat = (fud) ->
      alert fud

    eat food for food in ['toast', 'cheese', 'wine']


# chained comparisons

    cholesterol = 127
    healthy = 200 > cholesterol > 60



# embedding JS
If you need to... you can embed JS in CS with backticks

    hi = `function() {
      alert [document.title, "Hello JavaScript"].join(": ");
    }`



<https://github.com/sleepyfox/coffeescript-koans>

or

<git@github.com:Pavling/wdi-w7d4-coffeescript.git>