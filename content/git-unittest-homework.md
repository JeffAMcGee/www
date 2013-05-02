Title: Using Git and Unittesting in a CS Course
Category: General
Date: 2012-12-15


I was the TA for Texas A&amp;M's Information Retrieval class in 2012.
We took some interesting directions with the homework assignments, so I'd like
to spend some time talking about the experience for the benefit of other
educators.
The professor, Dr. Caverlee wanted change the existing set of homework
assignments to bring in some web development by combining the existing
homeworks into a small project.
The students would build a small search engine with features like PageRank and
clustering.

<!--
In my opinion, any computer science student that gets through their
undergraduate curriculum without learning half a dozen languages is getting
shortchanged.
-->

The information retrieval class is a senior elective, so most of the students
have experience in multiple languages before they start the class, and most of
them will be getting a job in the computer industry within the next year.
I suggested that we should bring in some industry practices into the class:
version control, unit tests, and a NoSQL database.
The class was always primarily about the computer science concepts in
information retrieval. The software engineering techniques were brought in to
improve the course, but were never the main focus.
Universities should not become technical schools, but adding in some
practical tools is beneficial.

## The Project

On the first week of class, we gave the students a simple frontend to the
search engine they would build&mdash;a single-page html application written in
Javascript that talked to a simple json REST API.
Over the course of the semester, the students built the backend of the search
engine that searched over a corpus of ~1,000,000 Tweets posted during the
landing of the Mars Science Laboratory (Curiosity).

They started by building a very simple search engine that searched through 100
tweets.
The first assignment only depended on Python 2.7 and [Bottle](http://bottlepy.org/).
To help people get started, we included bottle.py in the sample code, which
meant that the first assignment could be done with a vanilla Python
installation.
For later assignments, they had to install and use several libraries including
Nosetests, Numpy, and PyMongo.

The class had a mailing list on Google Groups where people could post about the
homework.
When people were having problems I often asked them "What happens when you visit
[http://localhost:8000/api/search?q=mars](http://localhost:8000/api/search?q=mars)
in your browser?"
The rest api helped me figure out the problems people were having.

## Version Control

Before the semester started, I built a working version of the project that
contained the solution to all six homeworks.

I created a git repository which I shared with the students to distribute the
assignments and the solution sets.
When we released an assignment, I would push a new branch to the server for
students to start working on.
After each assignment was due, I would push the solution to the homework.
Here's what the graph of commits in git looked like when homework 3 was
assigned, and the solution to homework 2 was posted:


    :::text
      * 8262870 (hw2sol) homework 2 official solution
    * | fe0c370 (HEAD, master, hw3) homework 3 - PageRank
    | * 0bfc1f3 Merge branch 'hw2' into hw2sol
    |/|
    | * e6cf0c8 (hw1sol) homework 1 official solution
    * | e0466b5 (hw2) homework 2 - vector space retrieval
    |/
    * 949a920 (hw1) homework 1 - boolean search

For every assignment after the first one, I gave students detailed instructions
on how to use git to either build off their solution to the previous
assignment, or use our official solution.
I wanted students who were proud of their work on the previous homework to
continue building on what they had.
But if someone did poorly on one assignment, it should not prevent them from
doing the rest of the assignments.

I tried to prevent merge conflicts by keeping my edits in one set of files and
all the edits from students in separate files.
For the most part, this worked; I only remember one email sent to the class
mailing list about merge conflicts.

The students submitted their assignments by creating a tarball containing just
their .git directory and submitting it to our school's existing system for
turning in assignments.
(We could have had the students push to private repositories, but I really
didn't want to deal with pulling from one server and pushing onto another on
the first week of class.)
Windows users had a lot of trouble creating tar.gz files, so I wrote a tiny
script that used python's tarfile module to create the tar.gz file.
It would be nice to use `git diff-files` make sure the working directory is
clean before the student submits the assignment.

On the fifth assignment, the students were supposed to create their own tests
in a new file.
They were supposed to `git add` the file.
Asking them to do that was a mistake.
About a third of the class did not `git add` the file.
We had to ask everyone to send us that one file if `git status` said it was not
committed.
I should have added and commented an empty file when I uploaded the original
homework.

## Unit Testing
For the first four assignments, I gave the students a few simple test cases to
check that their code worked.
The tests gave students quick feedback about their homework.
When I graded the homeworks, I ran the student's programs against a much more
thorough set of tests.
(I also used `git diff` to look at exactly what the students had done.)

In the final assignments, I made the students write their own tests to verify
that their code was correct.
The tests the students wrote were not as good as I had hoped.
There were tests that read in 10,000 tweets to test one query, tests that
didn't test anything, and tests that failed because of rounding errors.
It may have helped to spend more time talking about testing philosophy on the
mailing list or in the early assignments.

I've used nosetests without problems on my projects in the past, but the
students had quite a few problems running nosetests.
In particular, people on Windows and running multiple versions of Python seemed
to have more problems.
I wish I had not used nosetests, and instead gave everyone a short bash script
and DOS batch file that ran all the tests.

## Results
I think that making the project a bit more realistic helped with student
motivation.
The practical skills the students gained in this project will help them in
their future careers.

All of the homeworks were assigned during the first half of the course.
During the second half of the course everyone worked on team projects.
The teams put the code from their projects on GitHub so they could work
together and we could see their progress.
Many of the teams reused things they had learned from the homeworks in their
final projects.

There are some exciting changes happening in higher education caused by rising
tuition costs and the internet.
One of the most interesting developments are MOOCs, massively online open courses.
I think source control and unit testing could work well for a MOOC.
Source control allows advanced students to work on projects that are too big to
do in a simple web browser-based IDE.
Unit tests could give students instant feedback as they are working on an
assignment.
After they submit the assignment, the unit tests can augment other techniques
like peer feedback.
