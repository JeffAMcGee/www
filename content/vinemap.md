Title: VineMap
Category: Projects
Date: 2013-03-21


[VineMap](http://crowdy.cs.tamu.edu/vinemap/) is a quick project I did a few
days after Vine launched.
I use Twitter's streaming API to listen for links to Vines.
I then geocode the location field from the Twitter's user profile.
(I'm throwing away vines posted by users without understandable locations.)
I put the tweets in a Mongo capped collection, which I share via a simple REST
API.
A javascript program pulls the new tweets every 15 seconds.

