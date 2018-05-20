---
title: "Building a bot to post Instagram stories automatically to my blog"
date: 2018-05-20T02:44:35+05:30
---

TLDR; Made a bot that posts selected instagram stories automatically to my blog.

Source Code [is here](https://github.com/geekodour/storytoblog)
if you're looking for that.

I love instagram. I like posting stories and memes there and yesteday night
I felt the urgency(ironical) to post about education on my story.

I wrote a moderately long post in a story with small text. I realized this should've
been a blogpost aswell but I already posted the story and to get the text back I've
to do OCR on it.

So I built a bot to do that for me everyday.

First step, getting hold of my instagram stories. I did not want to use
facebook graph api because it's a PITA.
So instead using broswer cookies to make the API calls gets me the stories.
(See source for URLs if you want.)

> I got the API endpoints to look at by using network tab on dev tools.

Now I have the cookies and I am able to get all of my stories using a rest API endpoint.

Use tesseract ocr on all the stories to get the text from the posted stories.

Now, I cannot have all of my stories posted to my blog,
so there should me some kind of **filter**,

So I decided any story starting with `@@` will be considered a blogpost.
You can change the `@@` to anything you like in the source, or maybe make that
a env var?

And then I needed a **title**, First line starting with `#` will be the title.

That being done I am ready to post the filtered stories anywhere I want.
Github issues of my blog repository seemed a good fit for me.
So just using a personal access token to post an issue for the story with
**Instagram** label.
![](/img/instastory.png)

I put the app on heroku with a quart server instead of flask this time around.

For the scheduling i am using [Cronjobs service](https://cron-job.org/en/) which calls the heroku api once everyday.

./Lots of love, Keep hacking.
