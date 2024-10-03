+++
title = 'Phoenix First Impressions'
date = 2024-10-03T13:20:19+01:00
draft = true
+++
I've started working with [Phoenix](https://www.phoenixframework.org/)
recently, and it's kinda cool - using LiveView and having the client view
update dynamically without having to write any JavaScript is amazing the first
time you see it in action.

I've programmed in Clojure in the past, so using a functional language with
immutable data and pattern matching was not massively novel to me. With any new
language and framework there's a bunch of toe stubbing frustrations as you try
and fail to implement things that would be "trivial" in an environment you're
more familiar with (for me this is Ruby-on-Rails) - but this isn't surprising.

The part that was surprising was the change in perspective LiveView brings when
you're using JavaScript for the client.

The feature I was working on was to switch between two images which you click
on a button, as well as other elements on the page (like captions for the
images).

If I was implementing this in a typical backend/frontend architecture, then I'd
probably have the backend serve up a HTML page with the URLs + captions for the
two images, and then add some JavaScript to allow toggling between the two
images. 

The difference is that with LiveView - 
