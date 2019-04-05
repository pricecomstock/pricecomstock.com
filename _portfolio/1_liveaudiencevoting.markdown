---
layout: post
title: live audience voting
description: a web-app for live-voting with emoji
img:
tags: [comedy, development, testkitchen]
---

This is a voting system designed to let an audience use their smartphones to vote live during a show. I wanted to make it very visible and fun for everyone in the room, so I made a live, physics-based bubble chart where each audience member is represented by an emoji, which moves to different areas as they change their vote.

<video width="100%" controls autoplay>
<source src="{{ site.baseurl }}/img/2019/voting/voting-demo.mp4" type="video/mp4">
</video>


Anyways, it's easy to see in real time, and is especially fun when you can instantly see the votes changing in reaction to some event. So far, we've used it for a few different shows, all in the Test Kitchen this was built for:

I made a writeup of the first show we did with it [in this blog bost]({% post_url 2019-03-17-live-audience-voting %}).

Check it out [here](https://testkitchen.fun).
Or maybe [here](https://vote.comstock.space) if testkitchen.fun expired and I didn't renew it. Both of these point at the same server. Magic!