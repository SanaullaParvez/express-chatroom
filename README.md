# express-chatroom

Welcome to express-chatroom of the Node.js Tutorial Series: A chatroom for all! In this part, I will explain what node.js is, why you should pay attention to node.js and how to setup your machine.

## Install

```
git clone git@github.com:SanaullaParvez/express-chatroom.git && cd express-chatroom/
npm install
npm run start
```

##Node

__What is node? Why node?__

Node.js is a runtime environment and library for running JavaScript applications outside the browser. Node.js is mostly used to run real-time server applications and shines through its performance using non-blocking I/O and asynchronous events. A complete web ecosystem has been built around node.js with several web app frameworks and protocol implementations available for usage. It’s definitely one of the easiest and fastest way to develop real-time applications on the web today.

__Why use node?__

One word answer: JavaScript. JavaScript is an extremely popular language and is credited with being one of the driving forces that turned the web into the dynamic wonderland that it is today. What you can do in a browser nowadays, rivals all others!
JavaScript arose on the frontend but - thanks to the V8 JavaScript engine and the work of Ryan Dahl - you can now run networked JavaScript applications outside of the browser precisely to build web apps. Node.js lets you unify the programming language used by your app - no longer do you need a different language for your backend, you can use JavaScript throughout. If your background is in building and design websites and web app frontends in HTML, CSS and JavaScript, you don’t need to pick up another language to develop complex data-driven back-ends for your apps.
Node.js plays a critical role in the advancement of WebSockets as a method for real-time communication between the front and back ends. The use of WebSockets and the libraries building on that protocol such as Socket.IO have really pushed what is expected of web applications and lets us developers explore new ways to create the web.

###Hello World in Node.js

``` var http = require('http'); ```

Node.js has a simple module and dependencies loading system. You simply call the function “require” with the path of the file or directory containing the module you would like to load at which point a variable is returned containing all the exported functions of that module.

``` var port = process.env.port || 1337; ```

On this line, we want to determine on which port the HTTP server serving the HTML should run. If a port number is specified in the environment variables, we will use that one or we will simply use 1337.

``` http.createServer(function (req, res) { ```

We want to create a server to handle HTTP requests. We will also pass the createServer function a function callback containing two parameters to a handle each individual request and return a response. Take a look at Michael Vollmer’s article if you’ve never encountered callback functions in JavaScript. The request received is passed in the req parameter and the response is expected to written to the res parameter.

``` res.writeHead(200, { 'Content-Type': 'text/plain' }); ```

Any HTTP response requires a status-line and headers, to learn more about HTTP headers and how they work check out this article. In this case, we want to return 200 OK as the status response and to specify the content-type as plain text. We specify this by calling the writeHead function on the response object.

```res.end('Hello World\n');```

Once we are done writing the response we want to call the end function. We can also pass the final content through the end function, in this case we want to send the string “Hello World” in plain text.

``` }).listen(port); ```

We close off the callback and call the function listen at the port we defined earlier, this will start the server and start accepting requests sent to the defined port.

##Express

__What is Express?__

Express is a minimal, open source and flexible node.js web app framework designed to make developing websites, web apps and APIs much easier.

__Why use Express?__

Express helps you respond to requests with route support so that you may write responses to specific URLs. Express allows you to support multiple templating engines to simplify generating HTML.

###Explanation of app.js

```
var express = require('express');
var routes = require('./routes');
var user = require('./routes/user');
var http = require('http');
var path = require('path');
```
Lines 1 through 5 load various modules including express, http and path. What’s interesting is that we also load a module called routes (which will be explained later) and a module in the routes folder called user.

``` var app = express(); ```

On this line, we called the function express() which will create our app this app will be used when we decide to create a HTTP Server and it will be the object containing all the properties of our web application as well as the mapping between the URL received in a request and the function handling its response.

```
// all environments
app.set('port', process.env.PORT || 3000);
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'jade');
```

On these lines, we set various configuration parameters such as what port the server will run on (line 15) and in which directory the template html files will be found (line 16). On last line, we specify the templating engine that we want to use, in this case Jade. Jade is a popular templating engine that makes writing HTML extremely easy and without the extraneous syntax requirements of angle brackets (<>). You can change the templating engine to simply return HTML as is and not do anything further by replacing Line 17 with the following code:
> app.set('view engine', 'html');

```
app.use(express.favicon());
app.use(express.logger('dev'));
app.use(express.json());
app.use(express.urlencoded());
app.use(express.methodOverride());
app.use(app.router);
```

On these lines, we set various configuration parameters. You can find the meaning of each individual parameter by taking a look at the API documentation. The explanation of these configuration parameters is not required for this tutorial.

```
app.use(require('stylus').middleware(path.join(__dirname, 'public')));
app.use(express.static(path.join(__dirname, 'public')));
```

These lines are interesting as it is where we specify middleware to handle Stylus CSS sheets and HTML. Middleware is a layer that is automatically inserted into the function calls between receiving the request and returning a response. In this case, we are asking express to run the stylus middleware and the static middleware for all requests in which the URL specific a path inside the public folder of our project. We use to this server CSS and JavaScript verbatim and not execute a request function for that URL.

```
// development only
if ('development' == app.get('env')) {
  app.use(express.errorHandler());
}
```

In these lines, we are specifying to express to handle errors if the environment is set as development and not production. You don’t have to worry about these lines.

```
app.get('/', routes.index);
app.get('/users', user.list);
```

In these lines, we are finally mapping a URL path in a HTTP request to a specific function to handling the response. We will get back to this shortly.

```
http.createServer(app).listen(app.get('port'), function(){
  console.log('Express server listening on port ' + app.get('port'));
});
```

In these lines, we create a HTTP server and specify the port, along with a callback on success to say the server has been started.

##WebSockets & Socket.IO

__What are WebSockets?__

WebSocket is a protocol designed to allow web applications to create a full-duplex channel over TCP (i.e. to have bi-directional communication) between the web browser and a web server. It is fully compatible with HTTP and uses TCP port number 80. WebSockets allowed web applications to become real-time and support advanced interactions between the client and the server. It is supposed by several browsers including Internet Explorer, Google Chrome, Firefox, Safari and Opera.

__What is Socket.IO?__

Socket.IO is a simple JavaScript library and node.js module that allows you to create real-time bidirectional event-based communication apps simply and quickly. It simplifies the process of using WebSockets significantly. We will be using Socket.IO v1.0 to make our chatroom app.

``` npm install --save socket.io ```

###Adding Socket.IO to app.js

```
i.   var app = require('../app');
ii.  var http = require('http');
iii. app.set('port', '3000');
iv.  var server = http.createServer(app);
v.   var io = require('socket.io')(server);
vi.  var mongo = require('mongodb').MongoClient;
vii. server.listen(port);
```
The next step is to add socket.io to app.js. You can easily achieve this by replacing the following code.This will capture the HTTP server in a variable called server and pass that server so that the socket.io module can attach to it. The last code block takes the server variable and executes the listen function which starts the HTTP server.


```
1. io.on('connection', function (socket) {
2.  console.log('a user connected');

4.  mongo.connect('mongodb://127.0.0.1:27017/express', function (err, db) {
      var collection = db.collection('chat messages')
      var stream = collection.find().sort().limit(10).stream();
      stream.on('data', function (chat) { socket.emit('chat', chat.content); });
8.  });

10.  socket.on('disconnect', function () {
       console.log('user disconnected');
12.  });

14.  socket.on('chat', function (msg) {
15.    mongo.connect('mongodb://127.0.0.1:27017/express', function (err, db) {
         var collection = db.collection('chat messages');
         collection.insert({ content: msg }, function (err, o) {
           if (err) { console.warn(err.message); }
           else { console.log("chat message inserted into db: " + msg); }
         });
21.    });

22.    socket.broadcast.emit('chat', msg);
23.  });
});
```
* Ideally, we want to log a user joining the chatroom.`1-2` line easily helps us accomplish that by hooking a callback function to be executed on every single “connection” event via WebSocket to our HTTP server. In the callback function, we call console.log to log that a user connected.
* To do the same for when a user leaves, we have to hook up to the “disconnect” event for each socket, we do that by adding `10-12` line inside after the console log of the previous code block.
* Socket.IO gives us a function called `emit` that we can use to send an event. What we want to do is any message received on the “chat” channel in `14` line and broadcast it to all the other connections on this socket.
To do this, in `22` line we want to listen to the “chat” channel and call emit with the broadcast flag in the callback for the “connection” event.
Here a simple Socket.io instance with a single listener. The socket.on() method is used to register listeners, while the socket.emit() method is used to trigger the event.
* We want to import that module to be able to use the mongodb client object in app.js. You can add the following lines of code after the first require(‘’) function calls, such as on line `vi`.
* `15-21` line we want to connect to the database using the URI we have in the 'mongodb://127.0.0.1:27017/express' and once connected, we want to insert the chat message received in the socket connection.
* In `4-8` we should make sure to send the last sorted 10 messages received to the server so at the very least we can give them some context. To do that, we need to connect mongo.

###Powering Up the Send Button
```
1.  var socket = io();
2.  $('#send-message-btn').click(function () {
3.      var msg = $('#message-box').val();
4.      socket.emit('chat', msg);
5.      $('#messages').append($('<p>').text(msg));
6.      $('#message-box').val('');
7.      return false;
8.  });
9.  socket.on('chat', function (msg) {
10.     $('#messages').append($('<p>').text(msg));
11. });
```
__Line 1__
We want to create a socket and we can do that by calling the io() function which is in the socket.io-client.js file.

__Line 2__
Followed by that, we want to execute a function on the click of the send message button using the jQuery’s $ function and the click event handler.

__Line 3__
We will get the string value in the message box using jQuery’s $ function.

__Line 4__
This is where the interesting thing happens, we use the emit function on the socket variable we created in line 1 to send a message on the ‘chat’ channel containing the message box’s value.

__Line 5-7__
At this point, we will append the message in the message box to the #messages div so that the user can see the message was sent. We will assign the value of the message box to an empty string to clear it up.

__Line 9-10__
Now we want to make sure to append the message received on the chat channel from other users to the #messages div. The node backend will not resend the message that the client wrote back to itself but that’s alright because we added the message right away in the click function handler.
