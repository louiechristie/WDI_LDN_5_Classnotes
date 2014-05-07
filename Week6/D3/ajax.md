# Week 6 Day 3

## Outline

* AJAX


# What is Ajax?

- AJAX stands for Asynchronous JavaScript And XML
- We use it to do stuff in the background
  - Such as making our pages feel more responsive by not requiring a full page load. 
  - Or pulling in content after our page has loaded. means we dont have to refresh our page.

In reality, we don't use XML all that often any more. Often we use JSON, but nothing says it has to be.

- An AJAX request consists of a few things. 
  - We need a URL to call, and maybe some data to go with it. 
  - We need a callback function: something that runs when we get our data back. 
  - Generally, we need two: one that runs on success, and one that runs on failure. 


## Yahoofinance example

- Let's build a simple HTML page for a stocks page.
- And a simple Sinatra app to give me a quote for a given stock symbol. 

`touch main.rb`

In main.rb

```
require 'sinatra'
require 'sinatra/reloader' if development?
require 'yahoofinance'
require 'json'
```

```
get '/' do
  erb :index
end
```


```
get '/:symbol' do
  quote = YahooFinance::get_standard_quotes(params[:symbol])[params[:symbol]]
  @vals = {:updatedAt => DateTime.now}
  [:symbol, :name, :date, :time, :lastTrade].each { |s| @vals[s] = quote.send s }
  erb :quote
end
```


Now lets create a views folder

`mkdir views`
`cd views`

Create our index.erb

`touch index.erb`

In index.erb

```
  <h1>Stock Quote Generator</h1>
  <p>Enter a stock symbol in the box below to get a quote for it.</p>
```

Now lets create a `quote.erb`

In quote.erb

```
  <h1><%= @vals[:name] %>(<%= @vals[:symbol] %>)</h1> 
  <p>Last value: <%= @vals[:lastTrade] %> (at <%= @vals[:time] %><%= @vals[:date] %>)</p> 
  <p><em>Data last updated:<%= @vals[:updatedAt] %></em></p>

```

Now we need to create a `layout.erb` in out views folder

```
  <!doctype html>
  <html>
    <head>
      <title>The stock quote generator</title>
    </head>
    <body>
        <%= yield %>
      <form action="/" method="POST">
        <label for="symbol_input">Stock Symbol</label>
        <input type="text" id="symbol_input" name="symbol" placeholder="eg. AAPL" />
        <input type="submit" value="Query" />
      </form>
    </body>
  </html>

```

- This will give us a form input on all our pages

- When submiting the form we will get an error

- Why ?

- Becuase we have nothing to handle this route.

- Now lets update our main.rb

```
require 'sinatra'
require 'sinatra/reloader' if development?
require 'yahoofinance'
require 'json'

get '/' do
  erb :index
end

post '/' do
  redirect to('/' + params['symbol'])
end

get '/:symbol' do
  stock_symbol = params[:symbol].upcase
  quote = YahooFinance::get_standard_quotes(stock_symbol)[stock_symbol]
  @vals = {:updatedAt => DateTime.now}
  [:symbol, :name, :date, :time, :lastTrade].each { |s| @vals[s] = quote.send s }
  erb :quote
end
```

- Now lets add a little CSS

In out application directory (not in views)

`mkdir public`
`touch styles.css`

- In `layout.erb` between the **<head>** tags we need to add 

`<link rel="stylesheet" href="styles.css" />`

Now in `styles.css`

```
  body {
      max-width: 960px;
      margin-left: auto;
      margin-right: auto;
      font-family: sans-serif;
  }
  
  h1 {
    text-align: center;
  }
  
  form {
    border-top: 1px solid #777;
    padding-top: 0.3em;
  }
  
  .about {
    font-size: 80%;
  }
```

- Now the user walks away and gets a coffee, and when they come back... the page is out of date. 
- So maybe we could just make it update via JavaScript.
- But this 'locks' the browser

  ```
  var now = new Date();
  var curentDate = null;
  do { currentDate = new Date(); } while (currentDate-now < 5000);
  ```
- And means your other code can't run.
  
- So we can make it update via JavaScript in the background.
  - Browsers give you a think called the XMLHttpRequest object. Mostly... (IE)
  - We need an endpoint we can call. 
  - And we should request JSON data back. 
  - Now we've got it back, we need to do something with it. 
  - And we need to do something different if it fails. 

- And we need something that runs it. 
  - Hook it into the form button.
  
- How do we do this in jQuery? 
  - Include jQuery on the page. 
  - jQuery gives us a handy .ajax() method. 
    - It takes a bunch of parameters: 
      - url
      - complete - always runs
      - error - a function that runs on error
      - success - a function that runs on success
      - dataType - the type of the returned data that we're expecting. 
      - type - the HTTP method to use.
  
  
Create `application.js` in your public directory

We now need to add `<script src="application.js"></script>` to our layout.erb

our **head** in layout should now look like this
```
  <head>
    <title>The stock quote generator</title>
    <link rel="stylesheet" href="styles.css" />
    <script src="jquery-1.11.0.min.js"></script>
    <script src="application.js"></script>
  </head>
```

- In application.js

```
$(document).ready(function() {
  $('form').on('submit', function(ev) {
    ev.preventDefault();
    var symbol = $('#symbol_input').val();
    $.ajax({
      url: '/' + symbol,
      success: function(data) {
        console.log(data)
      }
    });
  });
});
```

Now into our `stocks.rb`

```
  require 'sinatra'
  require 'sinatra/reloader' if development?
  require 'yahoofinance'
  require 'json'
  
  get '/' do
    erb :index
  end
  
  post '/' do
    redirect to('/' + params['symbol'])
  end
  
  get '/:symbol' do
    quote = YahooFinance::get_standard_quotes(params[:symbol])[params[:symbol]]
    @vals = {:updatedAt => DateTime.now}
    [:symbol, :name, :date, :time, :lastTrade].each { |s| @vals[s] = quote.send s }
  
    if request.xhr?
      [200, {"Content-Type" => "application/json"}, JSON.generate(@vals)]
    else
      erb :quote
    end
  end

```

In `quote.erb`

```
  <h1><span id="name"><%= @vals[:name] %></span> (<span id="symbol"><%= @vals[:symbol] %></span>)</h1>
  <p class="quote">Last value: <span id="lastTrade"><%= @vals[:lastTrade] %></span> (at <span id="time"><%= @vals[:time] %></span> <span id="date"><%= @vals[:date] %></span>)</p> 
  <p class="about"><em>Data last updated: <span id="updatedAt"><%= @vals[:updatedAt] %></span></em></p>
```


In `application.js`

```
$(document).ready(function() {
  $('form').on('submit', function(ev) {
    ev.preventDefault();
    var symbol = $('#symbol_input').val();
    $.ajax({
      url: '/' + symbol,
      success: function(data) {
        console.log(data)
        $('#name).text(data.name);
      }
    });
  });
});
```

- Now when we submit our form on a stock page it will update the name.

- We can now update our application.js so that it will update everything else.

```
$(document).ready(function() {
  $('form').on('submit', function(ev) {
    ev.preventDefault();
    var symbol = $('#symbol_input').val();
    $.ajax({
      url: '/' + symbol,
      success: function(data) {
        var symbols = ['name', 'symbol', 'lastTrade', 'time', 'date', 'updatedAt'];
        for (var i = 0; i < symbols.length; i++) {
          update($('#' + symbols[i]), data[symbols[i]]);
      }
    });
  });
});
```
  - We can also do this differently, using function chaining. 
    - done(), fail(), always().

- Let's make this better. 
  - Progress indicator.
  - Error messaging
  - Highlight elements when we load them in. 
  
Update quote.erb to include `<p id="updatemessage" class="hidden">Updating...</p>`

quote.erb should now look like this.

```
  <h1><span id="name"><%= @vals[:name] %></span> (<span id="symbol"><%= @vals[:symbol] %></span>)</h1>
  <p id="updatemessage" class="hidden">Updating...</p>
  
  <p class="quote">Last value: <span id="lastTrade"><%= @vals[:lastTrade] %></span> (at <span id="time"><%= @vals[:time] %></span> <span id="date"><%= @vals[:date] %></span>)</p> 
  <p class="about"><em>Data last updated: <span id="updatedAt"><%= @vals[:updatedAt] %></span></em></p>

```

- Go to [ajaxload.info](http://ajaxload.info/) click **Generate it !**   

- Now click download.

- Put the file into your projects **public** directory.


Update the styles.css to include

```
#updatemessage {
  margin-left: 30%;
  margin-right: 30%;
  border-radius: 20px;
  border: 1px solid #aa3;
  padding: 5px;
  padding-left: 25px;
  background: url('NAME-OF-Ajaxload-Download.gif') 5px 5px no-repeat;
  background-color: #ee9;
}
```


We can now update our `application.js` to show and hide this when the form is submitted.


Add the following functions to the top of application.js

```
function showLoading() {
  $('#updatemessage').slideDown();
}

function endLoading() {
  $('#updatemessage').slideUp();
}
```

Update the $(document) to the following

```
$(document).ready(function() {
  $('form').on('submit', function(ev) {
    ev.preventDefault();
    var symbol = $('#symbol_input').val();
    showLoading();
    $.ajax({
      url: '/' + symbol,
      type: 'GET',
      success: function(data) {
        var symbols = ['name', 'symbol', 'lastTrade', 'time', 'date', 'updatedAt'];
        for (var i = 0; i < symbols.length; i++) {
          update($('#' + symbols[i]), data[symbols[i]]);
        }
      },
      complete: endLoading
    });
  });
});
```

- `complete` is similar to `ensure` in ruby


- What else can we do with AJAX? 
  - We can check for users posting new content, like Twitter showing '3 new tweets' at the top if you leave a page open. 
  - We can do *partial rendering*, where we render part of the page on the server and send the new content to the browser. 
    - Could do this for pagination. 
    - Could do this for switching between tabs. 
    - Can reduce server load times, because we don't have to generate the entire page all at once. 

- What's the point? 
  - Smaller web requests
  - Makes pages feel more 'alive'
  - Lets us update pages without forcing the user to hit refresh


# Additional resources

[updating a div in sinatra with jquery ](http://craiccomputing.blogspot.co.uk/2011/11/updating-div-with-jquery-and-sinatra.html)

GAChat
=====

- GAChat is a group chat system. You've been given a chat server that runs in Sinatra. 
  - It's got two actions: a `GET /` that renders the page, and a `POST /chat` that saves a line of text. 
  - Our `POST` is smart enough to cope with AJAX: if you don't have AJAX working, it does an HTTP redirect back to the home page. But if you do have it working, then it returns JSON that contains all the lines of text since the last time you said something.

- *Your mission is*: 
  - Create a JavaScript file that submits the lines of text supplied to the server, via AJAX. 
  - It should then update the display with the new lines of text.
  - Don't forget: when you submit later lines of text, it should only add the new ones back...

  - Considerations: 
    - What happens if it hits an error? 
    - Should you be able to submit with no name? With no line? 
    - What would help the user experience? 
      - Highlight your name when you get mentioned? 
      - Update the chat display based on a timer instead
    - How could you extend it? 
      - Private messaging? 
      - Make the server store lines? 
  - Try to do as much as possible in JavaScript. My version runs fine with the server supplied - but if you want private messaging, you might have to change the server... 
