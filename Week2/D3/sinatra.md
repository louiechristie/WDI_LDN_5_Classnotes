#SINATRA


Go to the [Sinatra](http://www.sinatrarb.com/) website. Sinatra is basically Rails Lite, and will make it easier to learn Rails.   
Sinatra uses Ruby code to create websites. It enables us to interact and display the results (as strings) of our Ruby programs directly in the browser, finally allowing us to move away from terminal.


##Installing Sinatra

We can install Sinatra using a couple of gems:  
`$ gem install sinatra`  
`$ gem install sinatra-contrib`


##Getting started with Sinatra

We can now create a "hi.rb" file. Let's write some code:

```
require 'sinatra'
require 'sinatra/contrib/all'

get '/hi' do
  'Hello WDI'
end
```

As we've now seen several times, we run this by typing in the terminal:  
`$ ruby hi.rb`

Now in Chrome, go to url: `localhost:4567/hi`

We have made our very first dynamic web page. Well done!

Lets add a little bit more to our "hi.rb" file - a homepage:

```
require 'sinatra'
require 'sinatra/contrib/all'

get '/hi' do
  'Hello WDI'
end
get '/' do
  'This is the homepage'
end
```

In Chrome, let's go back to our homepage url: `localhost:4567`

Whether we are in development, test or production, the environments we work in are different. We will switch between these depending on where we are in development. These environments also feature different error messages.

Sinatra also allows us to pass parameters, which we can use in our methods and display on our pages. For now, we will pass our parameters through the url.

Let's take a closer look at parameters:

```
require 'sinatra'
require 'sinatra/contrib/all'
get '/hi' do
  'Hello WDI'
end

get '/' do
  'This is the homepage'
end

get '/name/:first' do
  "hello, #{params[:first]}
end
```

Now we can go to our browser and go to our homepage url: 
`localhost:4567/name/Julien`

This should return the message:  
"Hello, Julien"

We can pass multiple parameters to be used in our methods:

```
require 'sinatra'
require 'sinatra/contrib/all'
get '/hi' do
  'Hello WDI'
end

get '/' do
  'This is the homepage'
end

get '/name/:first' do
  "hello, #{params[:first]}
end

get 'name/:first/:last/:age' do 
  "Your name is #{params[:first]} #{params[:last]}. You are #{params[:age]} years old."
end
```

Now we can go to our browser and go to our homepage url: 
`localhost:4567/name/luke/skywalker/13`

This should return the message:  
"Your name is luke skywalker. You are 13 years old."

We can also do calculations: 

```
require 'sinatra'
require 'sinatra/contrib/all'

get '/hi' do
  'Hello WDI'
end

get 'multiply/:x/:y' do
  (params[:x]to_f * params[:y].to_f).to_s
end
```

Now we can go to our browser and go to our homepage url:  
`localhost:4567/multiply/3/7`

This should return the message:  
"21.0"

To kill the Sinatra server, in terminal, type "Ctrl+C". To relaunch it, just use the command:  
`$ ruby hi.rb`

##MVC

**Model–View–Controller** (MVC) is a software architecture pattern which separates the representation of information from the user's interaction with it. 

![MVC Controller diagram](https://lh6.googleusercontent.com/XXRxMGxSc6n843q3K7-1qfG2YFHkM7Y_LOsHnBZRKwdTvYTw9rh9ama7ewdQjAf8Zu1J57CJKDwcmK0ueMQZp9c8Y1igJc33vYaOQBPboyUnQ6-eiJsFRNwrRA)

##Intro to erb

**ERB** stands for "Embedded Ruby" - Ruby is embedded in an HTML file in order to allow us to make our HTML files dynamic.

In our Sinatra App, we need to adapt our folder architecture. We also need to restructure our code as follows:

```
require 'sinatra'
require 'sinatra/contrib/all'
get '/hi' do
  'Hello WDI'
end

get 'multiply/:x/:y' do
  @result = params[:x]to_f * params[:y].to_f
  erb :calc
end
```

Now, we need to add a "views" directory to our folder. Inside that new folder, create a file called "calc.erb".
 
..views/calc.erb:

```
<doctype html>
<html lang='en'>
  <head>
    <meta charset="utf-8">
    <title>Calculator</title>
  </head>
  <body> 
    <p>
      The result is <%= @result %>
    </p>
  </body>
</html>
```

Now, let's switch back to Chrome and visit our homepage url:  
`localhost:4567/3/7`

This should return the message:  
"The result is 21.0"


##Adding a stylesheet

We can now style this page by making a "public/style.css". First, add the "public" directory, then create the "style.css" file within this newly created folder. 

We will need to link this stylesheet in our "calc.erb" file.

```
<doctype html>
<html lang='en'>
  <head>
    <meta charset="utf-8">
    <title>Calculator</title>
    <link rel=""stylesheet" href="/style.css">
  </head>
  <body> 
    <p>
      The result is <%= @result %>
    </p>
  </body>
</html>
```


In "style.css":

```
body {
  background: pink;
}
```

Since we are lazy, we do not want to have to re-type the whole "html", "head", "body", etc... with every new page we create. To solve this, we need to create a layout. 
We create this in our "views" folder.


##Introducing a layout

Layouts let us move the template parts of our HTML files to a single place, which is included in every view. 

By default, Sinatra looks for a view file called "layout" (but you can override that, and have different layouts for different things).

..views/layout.erb:

```
  <head>
    <meta charset="utf-8">
    <title>Calculator</title>
    <link rel=""stylesheet" href="/style.css"
  </head>
  <body> 
    <h1>My Calculator</h1>
    <%= yield %>
  </body>
</html>
```

We can change "calc.erb" to:

```
<p>
  The result is <%= @result %>
</p>
```

The contents of the "calc" file will be pulled into the layout file where we have written:  
`<%= yield %>`

We can create another page by creating a new file in views called "name.erb":

```
<p>
  Your name is <%= @name %>
</p>
```

And we can now change our controller to: 

```
require 'sinatra'
require 'sinatra/contrib/all'

get '/name/:first' do
  @name = params[:first]
  erb :name
end

get 'multiply/:x/:y' do
  @result = params[:x]to_f * params[:y].to_f
  erb :calc
end
```

From now on, when we go to `localhost:4567/multiply/:x/:y`, Sinatra will load the "calc.erb" file through our layout. 

Similarly, when we go to `localhost:4567/name/:first`, Sinatra will also load "name.erb" through our layout.

In a similar fashion, we can build an "about" page. 

In our "views" folder, let's create an "about.erb" page:

```
<h3> About page </h3>

<ul>
  <li><%= @name %></li>
  <li><%= @job %></li>
</ul>
```

Now, in "hi.rb", add:

```
get "/about" do
  @name = "Julien"
  @job = "developer"
  erb :about
end
```

##Introducing Forms

To take user input and introduce it in our workflow, we need to add forms to our web app. Let's update our "calc.erb" file and add a simple form. 

calc.erb:

```
<form method="POST" action="/calc">
  <ul>
    <li> First number: <input type="text" name="first_number"></li>
    <li> Operator: <input type="text" name="operator"></li>
    <li> Second number: <input type="text" name="second_number"></li>
    <input type="submit">
  </ul>
</form>

The result is <%= @result %>
```

Let's also change our "hi.rb" file to reflect these changes; get rid of the multiply method, and replace it with:

```
get "/calc" do
  erb :calc
end
```

Let's also add a "post" method:  
`post "/calc" dp`
`  p params `(this allows us to see, in the terminal, the hash of params passed)
`  erb :calc`
`end`

Now that we've tested that the params appear in terminal, let's change our "post":

```
post "/calc" do
  operator = params[:operator]
  first_number = params[:first_number].to_f
  second_number = params[:second_number].to_f
  @result = case @operator
    when "+" then first_number + second_number
    when "-" then first_number - second_number
    when "x" then first_number * second_number
    when "/" then first_number / second_number
  end
  erb :calc
end
```

Now when we go to `localhost:4567/calc` we should be able to enter values into our form, and be returned the result. 


##A few words on "Sessions"

Let's enable the sessions (we understand them as information being stored in cookies, which are persistent even after you've quit and reloaded your browser).

In order to do that, in your "hi.rb" file, right after the "require..", add:  
`enable :sessions`

Then update your "post":  

```
post "/calc" do

  session["calc_count"] = 0 unless session["calc_count"]
  session["calc_count"] += 1

  operator = params[:operator]
  first_number = params[:first_number].to_f
  second_number = params[:second_number].to_f

  @calc_count = session["calc_count"]

  @result = case @operator
    when "+" then first_number + second_number
    when "-" then first_number - second_number
    when "x" then first_number * second_number
    when "/" then first_number / second_number
  end
  erb :calc
end
```

Finally, in "calc.erb", add:  
`You've used the calculator <%= @calc_count %> time(s).`

We've now got a fully functioning "counter" that keeps track of the number of times we have been using the calculator. It will keep updating that number - until we delete the cookie.