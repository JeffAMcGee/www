Title: Sharing Models Between Node and the Browser
Category: General
Date: 2013-5-3

Imagine you're building a web app in Django, and you want the user to enter a
number between 1 and 100 on a web form.
Where do you put the code to check that the number is between 1 and 100?
If you want a slick user interface, you have to write code in Javascript to
verify the number.
If an invalid number could lead to security issues or other bugs, you have to
write the same code in Python to run on the server and verify the number.
Modern web development is not DRY; it's WET: Write Everything Twice.

I'm not entirely a fan of Javascript.
It's certainly not as ugly as PHP, but it's no Python.
I've been playing with node.js lately.
What originally attracted me to Node.js was the idea of sharing logic between
the client and the server.
This turned out to be harder than I expected.

In this blog post, I look at creating a simple node.js website that
communicates over socket.io and uses Knockout's models on both the client and
the server.
The demo program is a chess board with two black rooks that anyone visiting the
site can move around.

I will be using Express as backend website framework,
Mongodb for the database,
Underscore for functional programming,
Knockout as the MVC framework,
Socket.io for real-time communication, and
Browserify for bundling everything together and making CommonJS modules work in the browser.
(I wrote in detail why I choose to use Knockout, but it got to be so long that
I made it into a separate [post](/mvc-frameworks.html).)

## Sharing Models
I'm not going to go through all the details of developing for node.js and
Knockout, so I'm going to assume you've been through the tutorial for
[Express](http://expressjs.com/guide.html) and
[Knockout](http://learn.knockoutjs.com/#/?tutorial=intro).
If you'd like to run the code, you should can grab the
[source code](https://github.com/JeffAMcGee/ko-sock-demo) from Github.
Disclaimer: I'm not an expert at node.js so there are probably things that
could be done better.
This was mostly an experiment to play with some new technologies.

As I said previously, I'm going to build a simple chess board with two rooks.
So let's start out with the goal, and build everything around that:
* We want Javascript objects that work in both a browser and in Node.
* We want the fields in the object to be knockout observables.
* We want a function that can determine if a chess move is valid.

Let's do this by making two models: one to represent the game, and another to
represent each piece.

    :::javascript
    var ko = require('knockout');
    var BOARD_SIZE = 8;

    var GameModel = function(data) {
      this.players = ko.observableArray(["black","white"]);
      this.pieces = ko.observableArray([]);
    };

    var PieceModel = function(data) {
      this.owner = ko.observable("black");
      this.location = ko.observable([0,0]);

      this.isValidMove = function(move) {
        // check that move is a valid move for a rook.
        var loc = this.location();
        if( move[0]<0 || move[0]>=BOARD_SIZE || move[1]<0 || move[1]>=BOARD_SIZE ) {
          return false;
        }
        var vert = (loc[0]===move[0] && loc[1]!==move[1]);
        var horz = (loc[0]!==move[0] && loc[1]===move[1]);
        return vert || horz;
      };
    };

    module.exports = {
      GameModel:GameModel,
      PieceModel:PieceModel
    };

You should notice that this is a CommonJS module&mdash;it looks like code for
Node.js&mdash;it uses `require()` to import Knockout, and `module.exports` to
export `GameModel` and `PieceModel`.
Node expects code to be in CommonJS modules, but CommonJS is not a good format for a web browser.
We'll look at that fixing that next.

Browserify is a tool that will combine a bunch of CommonJS modules into a
single javascript file.
We'll add the commands to run Browserify in serve.js so that it runs each time
the development server starts up.
(In production, you will want to have Browserify bundle and minify the
Javascript as part of deployment.)
This code tells browserify to combine main.js and all of its dependencies into bundle.js:

    :::javascript
    var browserify = require('browserify');
    var browserFiles = browserify(['./static/js/main.js']);
    var bundle = browserFiles.bundle({});
    bundle.pipe(fs.createWriteStream('./static/js/bundle.js'));

So we now have a file models.js that contains Knockout models that run in
node.js and get bundled into a file that can run in the browser.

The next step is to hook up communication between the server and the client.
We need a way to convert the Knockout models into something that is
JSON-serializable.
I added a method toJS() which converts GameModel and PieceModel objects to
plain javascript objects.
I also modified the GameModel and PieceModel constructors to use the data
parameter.
Here's the updated PieceModel:

    :::javascript
    var PieceModel = function(data) {
      // construction
      if ( !(this instanceof PieceModel) ) { return new PieceModel(data); }
      var proto = { location:[0,0], owner:"black" };
      _.extend(proto, data);

      // properties
      this.owner = ko.observable(proto.owner);
      this.location = ko.observable(proto.location);

      // serialization
      this.toJS = function() {
        return {owner:this.owner(), location:this.location()};
      };
      // ... more stuff including isValidMove ...
    };

I did something similar with GameModel, so this code is starting to get a bit
boilerplatey for my taste.
The [Knockout Mapping](http://knockoutjs.com/documentation/plugins-mapping.html)
module may help with this.

## Using Socket.io
Now let's start talking between the client and the server.
In `routes/socket.js` we create a socket.io server that listens for connections
from the browser:

    :::javascript

    var models = require('../models');
    var game = new models.GameModel();

    module.exports = function (socket) {
      socket.emit('startup', game.toJS());

      // socket.on('move', ... );

    };

This code creates one global game that all clients will share.
That's fine for now, but in a real system you would obviously want to store
this game in a database of some sort.

When the server gets a new connection, it sends a serialized version of the
game to the browser.
In `static/main.js`, which runs on the browser, we wait for the server to send
us a copy of that game.
When we get the game, we call `ko.applyBindings` to start up Knockout:

    :::javascript
    socket.on('startup', function (data) {
      game = new models.GameModel(data);
      ko.applyBindings(game);
    });

We set up an event handler to detect when the user drags and drops a chess piece.
When the user moves a rook, we check that the move is valid by calling
`isValidMove` from `static/main.js`.
If the move is valid, we send a message to the server:

    :::javascript
    if(piece.isValidMove(loc)) {
      socket.emit('move', { id: bindingContext.$index(), location: loc });
    }

The server gets the move message with the piece id and the new location.
The server also checks that the move is valid.
If it is, then we send a message to all the clients to move the piece.

    :::javascript
    socket.on('move', function (data) {
      var piece = game.pieces()[data.id];
      if(piece.isValidMove(data.location)) {
        piece.location(data.location);
        socket.broadcast.emit('move', data);
        socket.emit('move', data);
      } else {
        console.error('invalid move');
      }
    });

All of the clients have code that recieves that message and moves the rooks:

    :::javascript
    socket.on('move', function (data) {
      game.pieces()[data.id].location(data.location);
    });


And there you have it. A tiny chess board with logic shared between the client
and server in Javascript.
The full [source code](https://github.com/JeffAMcGee/ko-sock-demo) (with
instructions) is on Github.
