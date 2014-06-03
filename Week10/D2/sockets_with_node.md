# Websockets

## Intro and recap
- Cast your minds back to week 1, day 1, hour 1. If you had to summarise how the internet works in one sentence, what would you say? 
  - Request/response is the answer we're looking for. 
  - Reminder that the internet is stateless.
    - The web server has no idea who you are when you make a request. 
    - Cookies get around that, by giving a client something to be remembered by. 
      - The server says "Here, hold this red hankerchief, and use it whenever you ask something." 
      - So when the client says "Can I have page X, please?" it waves the red hankerchief. 
      - And the server can say "Oh, I remember this guy with the red hankerchief, he's totally logged in." Or not, if not. 
  - And broadly speaking, the server drops the connection after every single file. 
  - That's HTTP, of course. That describes how web servers and browsers can talk to each other. But how does the data actually get there? 
  - We touched briefly on this; anyone remember TCP? IP? 
    - This stuff is more low-level than HTTP. HTTP sits on top. 
    - Our data gets broken down into packets, and each packet is sent across the network.
    - There's a thing that the web doesn't use, called UDP. User Datagram Protocol. 
    - It's really simple: basically just throws packets out onto the network, and forgets about them. 
      - It doesn't guarantee what order they'll be received. 
      - It doesn't guarantee IF they'll be received. 
      - Ideal for games! You just want the latest positions of all the world objects. If stuff gets dropped, we don't care. 
      - Good for VoIP too. If you get the occasional glitch or dropped packet, it's better than more latency.
    - Transmission Control Protocol is for stuff that we care a bit more about. 
      - Guarantees the ordering of data received.
      - Guarantees (as much as possible) that data will get through. 
      - How? 
        - Handshakes on connections. 
          - Server, are you ready? 
          - Yes, client, I'm ready.
          - Cool, here comes some data. 
          - (The data). 
  - So every single time we want to make a web request, we've got to do that handshake AS WELL AS the HTTP request. 
  - This is no big deal for web pages. 
  - But... what if we want to do a lot of requests? 
    - Get notifications when someone likes our cat picture on Facebook? 
    - Get new tweets for our timeline? 
    - Get chat messages? 
    - Get game actions for a multiplayer game? 
  - It's not so ideal any more, is it? 

## Websockets
- Hence, websockets.
- A web socket holds a connection open between the client and the server, and either end can send messages. 
- Your app no longer needs to say, regularly, "Got any new content?". When there's new content, it will receive a message.
  - Which sounds like a natural fit to event-driven programming to me...
  - You've changed your app from 'pull' to 'push'.
    - Pull: the client goes "Got any new content?" regularly. New data is pulled down by the client. 
    - Push: the client sits & waits until it gets told there is new content. 

## Let's have a go. 
- Have we talked about any software that's good for event-driven programming? 
  - Node.js! 
  - But this isn't a node.js lesson... we're going to skip over those bits fairly quickly. DEAL WITH IT

- Let's build a chat app. A chat app that doesn't poll for new content. 
- We're going to need an HTML page for this, so let's start there. 

- mkdir Classwork/socketchat
- cd Classwork/socketchat
- git init . 
- Let's create a simple Node app to serve our static files. 
  - We'll use express to help us: `npm install express`
  - edit server.js: 
    ```
    var express = require('express'); // Use the express library. 
    var app = express(); // Create our app. 

    var http = require('http');
    server = http.createServer(app); // Create an HTTP server.
    server.listen(process.env.PORT || 4000); // Listen on the default port, or on 4000 if there's not one.
    ```
  - `node server.js`
    - No output! And no output on the console! Node is very quiet. 
    - Visit http://localhost:4000/, "cannot GET /". 
      - But that means it's working! 
- Let's serve a static HTML file. 
  - mkdir views
  - echo "<h1>Hello world!</h1>" > views/index.html
  - update server.js: 
    ```
    var fs = require('fs'); // Allow us to read files. 
    app.get('/', function(request, response) { 
      fs.createReadStream('./views/index.html').pipe(response);
    });
    ```
    - If you don't have a file, your node server will quit! 
    - We read the file from the filesystem...
    - ... and pipe it into our response. 
    - Tell me again about how awesome Node is? 

- So now we see our "Hello world!" when we go to http://localhost:4000/, right? 
- Let's update our index.html to look more like a chat app. 
  ```
  <html>
    <head>
      <title>GAChat with web sockets</title>
    </head>
    <body>
      <h1>GAChat v2</h1>
      <p class="connecting">Please wait, connecting to server...</p>
      <form action="#">
      <input id="nick" type="text" placeholder="Enter your name" disabled="disabled" />
        <ul class="chatlines">
        </ul>
        <input type="text" placeholder="Enter text here" disabled="disabled"/>
        <input type="submit" value="Speak!" disabled="disabled" />
      </form>
    </body>
  </html>
  ```

- We're going to want some CSS and some JavaScript to power our app... Let's serve it. 
  - Add `app.use('/static', express.static(__dirname + '/static'));` to server.js. 
  - mkdir static
  - add `<link rel="stylesheet" href="/static/styles.css" />` to your index.html header.
  - add `body { background-color: red; }` in static/styles.css
  - Restart server; does it work? (It should.)
- CSS break!
  ```
  html {
    background-color: #eee;
  }

  body {
    max-width: 960px;
    margin-left: auto;
    margin-right: auto;
    font-family: sans-serif;
  }

  h1 {
    text-align: center;
    border-bottom: 1px solid #aaa;
    margin-bottom: 40px;
  }

  p.connecting {
    max-width: 400px;
    background-color: #dd9;
    padding: 10px;
    border-radius: 5px;
    margin: 0 auto 40px;
    text-align: center;
  }

  ul.chatlines {
    max-width: 600px;
    border: 1px solid #aaa;
    background-color: #ccc;
    min-height: 50px;
  }
  ```

- JavaScript. 
  - Drop jQuery into /static and include it in your page.
  - in /static/app.js: `alert("Lol");`
  - in index.html: `<script src="/static/app.js"></script>`
  - So it all works. We've got the scaffolding. 
  - Let's build our chat app!

# The state of websockets
- New technology, but not totally bleeding-edge.
- A standard exists...
- ... but that doesn't mean all the browsers have implemented it. 
- There are some third-party services that make this easier. 
  - Pusher
  - Something else? 
- But the whole point of us doing Node is that we don't need to use anyone elsewhere. 
- We can use a project called socket.io to do all of this ourselves. 

# Sockets.io setup
  - npm install socket.io
  - In server.js, at the end: 
    ```
    var io = require('socket.io').listen(server);
    io.sockets.on('connection', function(socket) {
        socket.emit('connected');
    });
    ```
    - Look familiar? 
    - When io.sockets emits a 'connection' event, emit a 'connected' event on the socket that was just connected. 
  - Add the socket.io JS to the client: `<script src="/socket.io/socket.io.js"></script>`
  - Check it's working with a simple callback: 
    ```
    var socket = io.connect('http://localhost:4000/');
    socket.on('connected', function() {
        alert("Connected!");
    });
    ```
  - This should work now. 
  - Update callback to remove the alert and do `$('.connecting').slideUp(); $('input').attr('disabled', false);` instead. 
  - This is essentially a handshake. 
    - The server sends HTML to the browser.
    - The browser requests CSS and JavaScript.
    - The JavaScript tries to establish a socket connection. 
    - When the server sees a new connection, we trigger a 'connected' event on the socket.
    - When the app sees a 'connected' event, we know we've got a socket open to the server, and we can allow the user to chat. 


# Let's build a chat app. 
- You can send things back onto one socket, or to all sockets. Which do you think we're going to use most? 
  - Broadcasts, because we want to send stuff to everyone.
  - Let's do our server first; when we see a 'chat' event, we should broadcast it to everyone. 
    ```
    socket.on('chat', function(data) {
      socket.broadcast.emit('chat', data);
    });
    ```
    - Remember to restart the server. 
  - OK, now the client. First, let's write a utility method that adds something to our chat area. 
    ```
    function writeLine(name, line) {
      $('.chatlines').append('<li class="talk"><span class="nick">&lt;' + name + '&gt;</span> ' + line + '</li>');
    }
    ```
    - Test it on the console. 
  - Call our new method when we get a 'chat' event. 
    ```
    socket.on('chat', function(data) {
      writeLine(data.name, data.line);
    });
    ```
  - Emit a 'chat' event when we send something. How did we do it last time? 
    - Stop the form submitting
    - Get the values in the 'nick' and 'text' boxes
    - Emit the event
    - Add our line to the display. 
    ```
    $(document).ready(function() {
      $('form').on('submit', function(ev) {
        ev.preventDefault();
        var $name = $('#nick');
        var $line = $('#text');
        socket.emit('chat', {name: $name.val(), line: $line.val()});
        writeLine($name.val(), $line.val());
        $line.val("");
    });
    ```
  - Test it! 
  - It works! 

# Different types of events
- Let's do some different events: some actions. 
  - HTML code: 
    ```
    <div class="actions">
      <button data-type="trout">Troutslap</button>
      <button data-type="rofl">Rofl</button>
      <button data-type="sad">:'(</button>
      <button data-type="boost">Boost</button>
    </div>
    ```
  - Update our client JS. 
    - Broadcast an event whenever we click a button (within document.ready): 
      ```
      $('.actions button').on('click', function(ev) {
        var $name = $('#nick');
        var $button = $(ev.currentTarget);
        socket.emit('action', {name: $name.val(), action: $button.data('type')});
        // writeAction($name.val(), $button.data('type'));
      });
      ```
    - Write a function to display our action: 
      ```
      function writeAction(name, action) {
        var actionStrings = {'trout': 'slaps the room around with a large trout',
                             'rofl': 'rolls around on the floor laughing',
                             'sad': 'looks rather sad :(',
                             'boost': 'scatters Boost around the room liberally.'};
        $('.chatlines').append('<li class="action">' + name + ' ' + actionStrings[action] + '</li>');
      }
      ```
    - Call it whenever we do an action (uncomment line above, add below): 
      ```
      socket.on('action', function(data) {
        writeAction(data.name, data.action);
      });
      ```

  - And update our server: 
    ```
    socket.on('action', function(data) {
      socket.broadcast.emit('action', data);
    });
    ```
  
  - Could tidy up the display a bit: 
    ```
    ul.chatlines li {
      list-style-type: none;
    }
    .nick {
      font-weight: bold;
    }
    .action {
      font-style: italic;
    }
    ```

# Private messages
- Each user gets its own socket. 
- So we'd store a mapping between the nick and the socket. 
- And when we wanted to message the user, we'd emit an event with a type of 'privatechat', a sender name, a recipient name, and the message. 
- And our server would look up the associated socket, and send the message just to them. 


