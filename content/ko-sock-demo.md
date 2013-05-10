Title: Sharing Models Between Node and the Browser
Category: General
Date: 2013-5-2

You're building a web app in Django, and you want the user to enter a number
between 1 and 100 on a web form.
Where do you put the code to check that the number is between 1 and 100?
If you want a slick user interface, you have to write code in Javascript to
verify the number.
If an invalid number could lead to security issues or other bugs, you have to
write the same code in Python to run on the server and verify the number.
Modern web development is not DRY; it's WET: Write Everything Twice.

I'm not entirely a fan of Javascript.
It's certainly not as ugly as PHP, but it's no Python.
What originally attracted me to Node.js was the idea of sharing logic between
the client and the server.
This turned out to be harder than I expected.
In this blog post, I look at creating a simple node.js website that
communicates over socket.io and uses Knockout's models on both the client and
the server.
Disclaimer: I'm not an expert at node.js so there are probably things that
could be done better.
This was mostly an experiment to play with some new technologies.

## Picking The Toolbox
Starting a project in Node.js feels a lot like reading a
Choose-Your-Own-Adventure book&mdash;every few minutes you have to choose
between a few options, and it's not obvious which one is the best.
Javascript has exploded in recent years, and there's lots of new libraries
springing up.
I'm sure the ecosystem will be much more mature in a year or two.

The first thing I needed to pick was a frontend MVC framework.
There were two things I was looking for: the ability to reuse code between the
client and the server and automatically updating views.
Here are the libraries I looked into:

* [Backbone](http://backbonejs.org/) - Backbone was the first project that
really tried to add structure to Javascript web applications.
There's a great
[blog post](http://blog.andyet.com/2011/feb/15/re-using-backbonejs-models-on-the-server-with-node/)
that describes how to share models between the client and the server in backbone.
Backbone does not have a built-in templating engine, but it is often used with
underscore's template.
(To be honest, I chose to not use backbone on this project mostly because I
wanted to experiment with shiny new toys, and I've used underscore's templates
for lots of things in the past.)

* [Meteor](http://meteor.com/) - I watched the video and poked around on the
tutorials, and I'm still not sure if I love it or I hate it.
I realize it can be turned off, but typing `db.book.find({year:{$gt:2010}})`
in the browser's console and getting back results from the MongoDB is crazy.
Meteor looks like it could be great for hackathons and rapid prototyping, but
I'm not sure if it lets you build maintainable apps.
In particular, they have their own protocol for talking between the server and
the client.
I really like the seperation of concerns that restful http over json enforces.
They also wrote their own package manager instead of using npm, and I wanted to
get to know npm.
I decided against meteor for this project.

* [Ember](http://emberjs.com/) - Ember is another up-and-coming web framework.
I spent some time trying to use Ember in Node, and I found some Stack Overflow
posts suggesting that this can't be done yet.
Their documentation is great on the theory about how their system works, but it
is really lacking in examples.
Both Angualar and Knockout have much better tutorials.
The Ember project has publicly said they are working on improving their
tutorials.
Ember is something I'd like to check up on in a few months.

* [Angular](http://angularjs.org/) - The new framework created by some people at Google.
When I first looked into Angular, it looked like it wouldn't work with node.
After using Angular on [another project](http://52weeks.jeffamcgee.com/), it
looks like you can use plain old Javascript objects as models in Angualar.
Someday I'd like to redo this demo in Angualar for comparison purposes.

* [Knockout](http://knockoutjs.com/) - I used the knockout library for this project.
There is a npm package that lets you use knockout inside a node app.
Knockout has great documentation, and it is more of a library than a framework.
I could generally figure out how things were implemented in
Knockout&mdash;there are very few things that seem magical.
My biggest gripe with knockout is that Knockout's models do not play nicely with prototypal inheritance.
You cannot put a `ko.observable` or a `ko.computed` on a prototype.

And that's just the first choice I had to make.
You see this same maze of twisty pasages every time you want to pick a new
library in Javascript.
Everyone seems to have written their own testing library.
I found eight npm packages for the jquery library, and most of them were
out-of-date.
There are dozens of tools for minification and deployment.
Even 10gen officially supports two different libraries for talking to MongoDB.
The Javascript community really needs a way to help people decide which libraries to use.

I'm going to fast-forward through the detailed analysis for the rest of the
things I looked at and just say I am using Express as backend website framework,
Mongodb for the database,
Underscore for functional programming,
Socket.io for real-time communication, and
Browserify for bundling everything together and making CommonJS modules work in the browser.

## Building it
I'm going to assume you've been through the tutorial for
[Express](http://expressjs.com/guide.html) and
[Knockout](http://learn.knockoutjs.com/#/?tutorial=intro).


The first thing that we need to create is package.json that lists all of our dependencies:

    :::javascript
    {
      "name": "knockout-socket-chess",
      "description": "demo app using knockout and socket.io",
      "version": "0.0.1",
      "private": true,
      "dependencies": {
        "browserify": "2.13.2",
        "express": "3.2.2",
        "knockout": "2.3.0pre",
        "mongodb": "1.3.0",
        "socket.io": "0.9.14",
        "socket.io-client": "0.9.11",
        "underscore": "1.4.4"
      }
    }


