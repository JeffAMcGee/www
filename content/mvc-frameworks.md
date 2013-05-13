Title: Picking Libraries in Javascript
Category: General
Date: 2013-5-2

This is a rant about the state of libraries in Javascript.
I recently built a small project in Node that shared code between the server
and the client.
One of the first steps was to pick the frontend framework
There were two things I was looking for: the ability to reuse code between the
client and the server and automatically updating views.

Starting a project in Node.js today feels a lot like reading a
Choose-Your-Own-Adventure book&mdash;every few minutes you have to choose
between a few options, and it's not obvious which one is the optimal.
Javascript has exploded in recent years, and there's lots of new libraries
springing up.
Most these libraries have not been battle-tested like JQuery or D3 have.
I'm sure the ecosystem will be much more mature in a year or two.

Anyway, here are the MVC libraries I looked into:

* [Backbone](http://backbonejs.org/) - Backbone was the first project that
really tried to add structure to Javascript web applications.
There's a great
[blog post](http://blog.andyet.com/2011/feb/15/re-using-backbonejs-models-on-the-server-with-node/)
that describes how to share models between the client and the server in Backbone.
Backbone does not have a built-in templating engine, but it is often used with
underscore's template.
(To be honest, I chose to not use backbone on this particular project mostly
because I wanted to experiment with shiny new toys, and I've used underscore's
templates for lots of things in the past.)

* [Meteor](http://meteor.com/) - I watched the video and poked around on the
tutorials, and I'm still not sure if I love it or I hate it.
I realize it can be turned off, but typing `db.book.find({year:{$gt:2010}})`
in the browser's console and getting back results from the MongoDB is crazy.
Meteor looks like it could be great for hackathons and rapid prototyping, but
I'm not sure if it lets you build maintainable apps.
In particular, they have their own protocol for talking between the server and
the client.
I really like the separation of concerns that restful http over json enforces.
They also wrote their own package manager instead of using npm, and I wanted to
get to know npm.
I decided against meteor for this project.

* [Ember](http://emberjs.com/) - Ember is another up-and-coming web framework.
I spent some time trying to use Ember in Node, and I found some Stack Overflow
posts suggesting that this can't be done yet.
Their documentation is great on the theory about how their system works, but it
is really lacking in examples.
Both Angular and Knockout have much better tutorials.
The Ember project has publicly said they are working on improving their
tutorials.
Ember is something I'd like to check up on in a few months.

* [Angular](http://angularjs.org/) - The new framework created by some people at Google.
When I first looked into Angular, it looked like it wouldn't work with node.
After using Angular on [another project](http://52weeks.jeffamcgee.com/), it
looks like you can use plain old Javascript objects as models in Angular.
Someday I'd like to redo the demo in Angular for comparison purposes.

* [Knockout](http://knockoutjs.com/) - I used the knockout library for this project.
There is a npm package that lets you use knockout inside a node app.
Knockout has great documentation, and it is more of a library than a framework.
I could generally figure out how things were implemented in
Knockout&mdash;there are very few things that seem magical.
My biggest gripe with knockout is that Knockout's models do not play nicely with prototypal inheritance.
You cannot put a `ko.observable` or a `ko.computed` on a prototype.

And that's just the first choice I had to make.
You see this same maze of twisty passages every time you want to pick a new
library in Javascript.
Everyone seems to have written their own testing library.
I found eight npm packages for the JQuery library, and most of them were
out-of-date.
There are dozens of tools for minification and deployment.
Even 10gen officially supports two different libraries for talking to MongoDB.
Good grief!

When I've worked in Java, C++, Perl, and Python it always seemed much more
obvious which libraries are the best ones to use for the job I was doing.
This is particularly true for languages with large built-in libraries like Java
and Python.
Since lots of libraries are already included, Python developers don't have to
decide which implementation of itertools to use.
(One place in Python-land where this is not true is web frameworks. There is
Django and then dozens of others.)
Anyway, I think competition in Javascript is good, but my hunch is lots of
decisions are made for reasons other than technical excellence.
The Javascript community really needs a way to help people decide which libraries to use.

