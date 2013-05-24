Title: Finding Local Political Conversations on Twitter
Category: Projects
Date: 2013-05-22

I think it would be cool to find local crowds of people discussing a topic
online.
What are people in Brazos county saying about Obama?
Who in my town is having interesting philosophical discussions?
What are people in Austin saying about Starbucks?

While working towards that goal, I did my masters thesis research on location
prediction in social media.
I built a system for predicting the location of users on Twitter based on their
friends and followers, [FriendlyLocation](https://github.com/jeffamcgee/friendloc).
This is a proof-of-concept I built on top of FriendlyLocation.

During the 2012 Republican and Democratic conventions I used the Twitter
Streaming API to find millions of tweets that contained terms like obama,
romney, and dnc2012.
I used FriendlyLocation to estimate the locations of the 2.5 million users who
posted these tweets.
I made a large directed graph that represented the communication between the users.
The users were the nodes of the graph.
If one user mentioned another user, then there was an edge between them.
I used the Louvain algorithm for community detection on that graph, which gave
me crowds of people who talked with each other and lived in the same area.


There is a <a href="">demo</a> you can play with on one of Infolab's servers.
Each of the circles on the map represents a crowd.
If you click on the circle, you can see the tweets that the people in the
crowds posted.
If you zoom in on the map, you can see more crowds.
Red crowds used more Republican terms, and blue crowds used more Democratic
terms.

It is a neat proof-of-concept, but there are still some open research questions
here:

* How do you detect interesting crowds?
* How do you deal with crowds in dense urban areas like New York City? (There
  can be hundreds of people all connected to each other in these places.)
* There's a lot of low-quality tweets. Filtering them out would be nice.
* People mention popular accounts like @barackobama even if they aren't
  actually talking with them. I'm currently removing the handful of users that
  got more than 50 mentions since those users aren't generally having
  conversations. There's got to be a better way to do this.

