---
layout: post
title:  generating wallpapers for inspopaper
date:   2019-04-07 12:00:00
description: easily the 5th or 6th best way to generate random inspirational wallpapers I could have written
---

[Inspopaper](https://inspopaper.fun) is a [Slack app I wrote](/portfolio/2_inspopaper/) that can generate random inspirational wallpapers featuring quotes of Slack messages.

<img class="fullimg" src="/img/2019/wallpaper-samples/water.jpeg">

## Generating Wallpapers
I'll keep this at a pretty high level, but I essentially use the following process:
* Get image from random Wallpaper API
* Use Python [Pillow](https://pillow.readthedocs.io/en/stable/) library to rasterize message with attribution using their name from Slack
* Break image down into a grid of ~100 overlapping boxes the size of the rasterized message text. Analyze each one using STDEV pixel color as a rough approximation of detail (less change in pixel color = less detail = good place to put quote to be readable) and choose the least detailed section as the quote location. If the section is not below a certain threshold STDEV, flag that this wallpaper should have a background box on the quote.
* Generate palette of black, white, and a few colors from the image. Pick one that is high contrast to the average color of the image section chosen above.
* Rasterize message in chosen color and render image with text placed at the chosen coordinates

## Technology Stack
Inspopaper currently runs on Google Cloud Platform and consists of 5 (see Lessons Learned) separate resources:
* Google Cloud Storage Bucket - for saving and hosting wallpapers
* Google Cloud SQL Database - for saving and referencing Slack Workspace API keys
* 3x Google Cloud Functions (hey, I said _see Lessons Learned!!!_)
    * **Inspopaper OAuth** - Endpoint that handles the [Add to Slack button](https://api.slack.com/docs/slack-button) request and writes the API key into the database.
    * **Wallpaper Slack Responder** - Endpoint that accepts the [message action request](https://api.slack.com/actions) from Slack. Unfortunately, instead of doing something a reasonable, normal function would do, it sends an HTTP request to the next function, intentionally times out right away, then responds to Slack within the required 3000ms with an ephemeral message confirming that the wallpaper is being generated.
    * **Wallpaper Bot** - The function that actually generates the wallpaper. It receives a callback URL from the Wallpaper Slack Responder function, and uses an HTTP POST request to send the wallpaper to that URL, which posts to Slack.

The main conflict leading to the weird setup I ended up with here is that Slack requires a response to commands within 3 seconds, but the wallpaper creation takes longer at least 50% of the time. I briefly toyed with the idea of caching results from the random wallpaper API to speed up wallpaper creation, but since the bulk of the time was the processing of the image, that didn't seem like it would reliably get results within 3 seconds.

Luckily, Slack provides a callback URL that can be used to send responses to a command for up to 30 minutes after the command. If there isn't a response to the initial request, an error will still appear to the user, which caused a lot of pre-emptive retries and double wallpapers during testing. In [Flask](http://flask.pocoo.org/) (which is used both by Google Cloud Python Functions [I'm pretty sure, it's been a while, don't @ me] and my original host, [PythonAnywhere](https://www.pythonanywhere.com/)), there is no way to execute code after sending a response. PythonAnywhere does not support threading, and my attempts to start threads from GCF technically worked, but the threaded code was taking between 1 and 10 _minutes_ to respond in Slack.

So, after chasing this bug and trying solutions that made sense, I arrived at the immediate timeout request to a second GCF, which is a pretty good solution if your metric is "not having to undo all the work you put into the wrong platform."

## Lessons Learned
When I created this, I wanted to learn serverless. I would now say Inspopaper is a little too complicated for a serverless architecture. It works, but I should have done more planning and realized that I needed more than one API endpoint and the ability to launch asynchronous processes from the API endpoint. It would have been easier to just run in a normal environment instead of 3 separate Google Cloud Functions. I still think Google Cloud Storage is probably the right platform for serving the images.

## Future Improvements
I can think of a few tweaks that could make it better:
* Offering a few wallpaper choices instead of just picking one
* A more controlled wallpaper source, although the random anime/low-res/not-very-aesthetically-pleasing ones are part of the fun
<img class="fullimg" src="/img/2019/wallpaper-samples/starcraft.jpeg">
* More fonts and formats. For example, the type of inspirational photo with much larger text covering the middle of it.