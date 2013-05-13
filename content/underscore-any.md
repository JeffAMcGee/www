Title: The History of One Line in Underscore.js
Category: General
Date: 2013-03-23

Programmers often read through code one function at a time or trace through a
program following the path of execution.
Today, I'm going to look at a program in a different way: I'm going to look at
how one line changed over time.
The line I'm going to look at comes from the `_.any()` method in the underscore.js library.
Why am I focusing on this line?
I found and fixed a bug on that line about a year ago.
While it seems like a simple task, several important Javascript pitfalls show
up in this one line.

It started out, like many web development problems do, with the observation
that a feature worked everywhere except Internet Explorer.
The `_.any()` method was always returning false in IE 8.
For those of you who don't know, the `_.any()` method calls a callback on every
element in a list.
If the callback returns true for any of the elements, then `_.any()` returns true.
On modern browsers, underscore makes the native method
[`array.some`](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Array/some)
do all the heavy lifting, but on older browsers, like IE 8, underscore uses its
own implementation.
It is obviously a very simple function&mdash;the kind of thing done in intro to computer science courses.
Even though it seems simple, there have been almost a dozen changes made to this function over the years.
To get started, let's jump in our TARDIS and look at the original version of `_.any()` from the
very first commit to underscore's git repo:

    :::javascript
    any : function(obj, iterator, context) {
      if (obj.some) return obj.some(iterator, context);
      var result = false;
      _.each(obj, function(value, index) {
        if (result = !!iterator.call(context, value, index)) throw '__break__';
      });
      return result;
    },

In this post, I'm going to focus on that if statement inside the loop:

    :::javascript
    if (result = !!iterator.call(context, value, index)) throw '__break__';

First, it calls the function iterator with the value and index.
Next, the `!!` converts iterator's return value to a boolean which is stored into the variable result.
If the value is true, it throws an exception which `_.each()` catches.
Do you see anything wrong with this?
It seems pretty reasonable, but there's still a lot that will change about it.

The first change was to make the iterator optional.
Now `_.any()` can be called without an iterator if you just want to test the
truthiness of each of the elements in the input list:

    :::javascript
    if (result = iterator ? iterator.call(context, value, index) : value) throw '__break__';

Next, as an optimization, they moved the check for the iterator out of the loop:

    :::javascript
    if (result = iterator.call(context, value, index)) throw '__break__';

They added the list to the parameters sent to the iterator. This makes it
behave more like JavaScript's official `Array.some`, and fixes issue
[#2](https://github.com/documentcloud/underscore/issues/2).

    :::javascript
    if (result = iterator.call(context, value, index, list)) throw '__break__';

There were several methods that had the expression `throw '__break__'`, so they
factored it out into a new method `_.breakLoop()`:

    :::javascript
    if (!(result = result && iterator.call(context, value, index, list))) _.breakLoop();

Someone complained that when an exception was thrown in `_.each`'s iterator you
would not see the full stack trace.
Instead, the stack trace would end inside forEach's catch block.
This is really a shortcoming of JavaScript's exception handling: you can't
catch specific types of exceptions.
(Browser debuggers could also be improved to cleanly handle the case of an
exception that gets rethrown in a catch block.)
The underscore developers changed forEach look for a specific sentinel.
If the iterator sent to forEach returns breaker, then the loop would end.
As a result, the developers changed `_.any()` to return the sentinel instead of
throwing an exception:

    :::javascript
    if (result = iterator.call(context, value, index, list)) return breaker;

The only problem was that the native version of `Array.forEach()` doesn't
recognize underscore's sentinel value breaker.
If you called any in an environment with a native `Array.forEach()`, but not
`Array.some()`, any would always return the value of the last item in the list
instead of true or false.
So they update result with the last true value we have seen.

    :::javascript
    if (result |= iterator.call(context, value, index, list)) return breaker;

Here's where I ran into trouble.
The bitwise or works fine for numbers, but it breaks when you have strings in the mix.
The documentation for underscore says it works for strings.
I submitted a pull request with two additional test cases and this proposed change:

    :::javascript
    if (result = result||iterator.call(context, value, index, list)) return breaker;

The people reviewing the pull request suggested changing the statement so that
it could short-circuit the assignment.  Here is the version that was merged in:

    :::javascript
    if (result || (result = iterator.call(context, value, index, list))) return breaker;

A few months later, someone decided it was messy and cleaned it up:

    :::javascript
    if (result = iterator.call(context, value, index, list)) return breaker;

The only problem was the tests didn't pass, so they reverted the commit a few minutes later:

    :::javascript
    if (result || (result = iterator.call(context, value, index, list))) return breaker;

So we're now at the end of the line. To put it in context, here's the current
source code for `_.any()`:

    :::javascript
    var any = _.some = _.any = function(obj, iterator, context) {
      iterator || (iterator = _.identity);
      var result = false;
      if (obj == null) return result;
      if (nativeSome && obj.some === nativeSome) return obj.some(iterator, context);
      each(obj, function(value, index, list) {
        if (result || (result = iterator.call(context, value, index, list))) return breaker;
      });
      return !!result;
    };

What can we learn from this?

1. Simple things can be hard to get right.
2. IE 8 is a pain.
3. Regression tests are awesome.

I suppose we really didn't learn that much here, but at least it was fun.
