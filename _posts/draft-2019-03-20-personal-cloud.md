---
layout: post
title: my easy-deploy, docker-based, personal cloud
date: 2015-07-15 15:09:00
description: how I used traefik and docker to make it easy to get new projects onto the internet as quickly as possible
---

## The Problem

When I was working on the [live audience voting system](/2019/03/17/live-audience-voting.html) I ran into a big issue. Or rather, _after_ I was working on it.

I thought the hard part was going to be, you know, learning [D3](https://d3js.org), [socket.io](https://socket.io), and trying out some other javascript features I'd not really worked with before.

Nope, turns out it was getting the damn thing onto the internet. It's been a while and I don't remember all the details, but I remember expecting it to be fairly easy. At the time, I was using [Dokku](https://github.com/dokku/dokku), which is basically self-hosted Heroku. I was running it on a Digital Ocean server. Honestly, it works well enough for most things, but it is somewhat opaque and I found it sort of difficult to troubleshoot when things went wrong, and it's now my opinion that I would have been better of just buckling down and really learning Docker and Docker Compose sooner. Turns out, going through their [getting started](https://docs.docker.com/get-started/) and actually following along with the steps on a test machine taught me quite a bit.

Anyways, when my friend Cory (the other host of [Test Kitchen](portfolio/9_testkitchen/)) and I were supposed to do a test run-through of the voting software, I tried for an hour to deploy it to Dokku and ended up giving up and running it locally for the demo. Between the test and the show, I spent probably 8 hours on troubleshooting the deploy. I'm writing this a few months after this harrowing experience but here's a quick list of complications I had that are largely solved by more directly controlling my builds and environments with Docker.

* Building the Vue single-page app requires dev tooling that is generally expected to be installed globally. I got lots of errors that were either known unresolved bugs, or had solutions that I couldn't easily apply within the dockku framework.
* Dokku is based off of Heroku buildpacks and used a different version of Nodejs (6ish) from the one I was developing with locally (10ish), with no easy way that I could find to manually tell it to use a different version. I believe this caused problems with at least one of the libraries I was using.
* My own terribly thought out setup for client-side environment variables at the time (like the URL of the backend API) in development and production.

## The Solution

At the time, I ended up solving these by just spinning up a whole new VPS and running only the voting system on it. Since then, I've started using a system that's closer to barebones Docker and lets me build the images separately instead of relying on interfacing with Dokku. Then if the image can run in a container on my machine, it should be able to do the same in the cloud.

I definitely did not come up with this on my own. I followed a couple different resources I found online. [Funky Penguin's Geek Cookbook](https://geek-cookbook.funkypenguin.co.nz/) is a super thorough guide for creating a swarm/cluster that can be easily deployed to. I ended up simplifying a lot of it and only using a single server so I wouldn't need to deal with shared storage or the increased complexity. What I ended up with is closer to [this](https://serdmanczyk.github.io/post/2017-08-25-Server/).

#### Template docker-compose.yml
{% highlight yaml %}
version: "3"
services:
  web:
    restart: always
    image: <pricecomstock>/<IMAGENAME>
    networks:
      - reverseproxy
      - default
    labels:
      - "traefik.enable=true"
      - "traefik.backend=<PROJECTNAME>"
      - "traefik.frontend.rule=Host:<URLS SEPARATED BY COMMAS>"
      - "traefik.docker.network=traefik_reverseproxy"

networks:
  reverseproxy:
    external:
      name: traefik_reverseproxy
{% endhighlight %}
