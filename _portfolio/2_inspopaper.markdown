---
layout: post
title: inspopaper
description: inspirational wallpapers bot-made from slack messages
img:
tags: [development]
---

[inspopaper.fun](https://inspopaper.fun)

Inspopaper is a slack app that adds a message action that allows anyone to create an inspirational wallpaper from any message.

## Technology Stack
Inspopaper currently runs on Google Cloud Platform and consists of 5 (see Lessons Learned) separate resources:
* Google Cloud Storage Bucket - for saving and hosting wallpapers
* Google Cloud SQL Database - for saving and referencing Slack Workspace API keys
* 3x Google Cloud Functions (I said _see Lessons Learned!!!_)
    * **Inspopaper OAuth** - Endpoint that handles the [Add to Slack button](https://api.slack.com/docs/slack-button) request and writes the API key into the database.
    * **Wallpaper Slack Responder** - Endpoint that accepts the [message action request ](https://api.slack.com/actions) from Slack. Unfortunately, instead of doing something a reasonable, normal function would do, it sends an HTTP request to the next function, intentionally times out right away, then responds to Slack within the required 3000ms with an ephemeral message confirming that the wallpaper is being worked on.
    * **Wallpaper Bot** - The function that actually generates the wallpaper. It receives a callback URL from the Wallpaper Slack Responder function, and uses an HTTP POST request to send the wallpaper to that URL, which posts to Slack.


## Lessons Learned
When I created this, I wanted to learn serverless. I would now say Inspopaper is a little too complicated for a serverless architecture. It definitely works, but I should have done more planning and realized that I needed more than one API endpoint and the ability to launch asynchronous processes from the API endpoint. It would have been easier to just run in a normal environment instead of 3 separate Google Cloud Functions. I still think Google Cloud Storage is probably the right platform for serving the images.

## Future Improvements