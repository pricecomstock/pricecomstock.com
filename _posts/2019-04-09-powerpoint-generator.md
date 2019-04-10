---
layout: post
title:  how to generate powerpoint presentations the "easy" way
date:   2019-04-09 12:00:00
description: ok, turns out it wasn't that easy but I could have made it a lot harder for myself by using powerpoint
---
A couple of years ago, I was invited to present at a panel at an anime convention. My topic was the *very exciting* [War of 1812.](https://en.wikipedia.org/wiki/War_of_1812)

Ok, it doesn't sound *very exciting*, but I and the other ~6 speakers on the panel all chose our topics a few minutes before presenting, and presented using slides we had never seen before, created by someone completely unaware of what we would be presenting with.

Turns out, this idea has [been around for a while](https://en.wikipedia.org/wiki/PowerPoint_Karaoke) and goes by a few different names like [Powerpoint Karaoke](https://www.powerpointkaraoke.com/) or Battledecks.

It was pretty fun, but I felt like there were a couple of issues. The big one is that creating the presentations takes a while. Even though the slides don't require research or even need to make sense, it is still a pretty large time sink and requires decisions by the author of the slides and not just the presenter.

So, I made a [Random PowerPoint Generator](https://pptx.pc.codes) for debut at another PowerPoint Karaoke style show at [Test Kitchen]({% link _portfolio/9_testkitchen.markdown %}).

## How to display random slides
When I started this project, I was pretty unsure of how I wanted to present the slides.

#### Finding a presentation framework
One unexpected issue with the PowerPoint Karaoke at that anime convention: we started 20+ minutes late because the laptop at the venue was for whatever reason unable to open the slides. The host tried various file conversions and using Google Slides instead of PowerPoint, but it took a while to get the slides onto the projector screen, and then all the performers were rushed for time.

I went looking for JavaScript presentation frameworks so I could keep it all in the web browser, but still get some "PowerPointy" features like animations. I started to realize that ideally, I would find a presentation framework that ran entirely in the frontend and could display a slideshow created in a JSON format. It would be easier to generate the data part of the slides and any settings I wanted to randomize, and then plug the big block of JSON into the presentation framework and be ready to go.

I looked into a [few](https://github.com/zulko/eagle.js/) [frameworks](https://github.com/hakimel/reveal.js/), but they all seemed to have drawbacks or architectural issues that would make random generation difficult.

Finally, I realized for the subset of presentation features I actually needed, it wouldn't be tough to just, you know, write a few basic slide types in HTML and use JavaScript* to render HTML based on a JSON schema of the presentation.

#### My "custom" JavaScript* "presentation" "framework"
_Okay_, I said "JavaScript" and that's technically true, but I used [Vue.js](https://vuejs.org/) because I have a boilerplate website created in it and it makes development easier, even if it's overkill for some projects. (can u tell i read hacker news comments and feel bad about my liberal use of SPAs?)

Anyways, I came up with a few different types of slides and created a component for each of them. I then created a parent Presentation component with a data property containing the Presentation object. This object contains an array of Slide objects, each of which has a type property that determines which component it's rendered as. Fun fact, I didn't use as much inheritance/DRY style between the slide components as I should have!

The Presentation component keeps track of the current slide index and displays the slide at that index, with a little extra magic to preload all images in the presentation and keep the Slide components around in case the presenter goes back a slide.

Unfortunately, it happened after the initial run, but I also went back and added support for random slide textures and color gradients with a settings object in the Presentation object.

## How to make random slides sort of good
With the frontend ready to display whatever the JSON told it to, I could start generating slides. I wanted a pretty wide variety of text and images and ended up finding a variety of useful sources:
* Wikipedia has a free API and built-in random function that does not require any authentication and can be used for images, page titles, and other more context-dependent text
* Reddit will return json by simply adding [.json](https://reddit.com/.json) to the end of the URL, no authentication required. There are also plenty of strange subreddits with good out of context content. /r/
  * [/r/nostupidquestions](https://www.reddit.com/r/nostupidquestions)
  * [/r/wtfstockphotos](https://www.reddit.com/r/wtfstockphotos)
  * [/r/relationships](https://www.reddit.com/r/relationships) and other advice subreddits
* An [API that returns facts about random numbers](http://numbersapi.com/#42)
* A few large data sets:
  * Jeopardy questions, answers, and categories
  * Questions to ask in a job interview/on a first date

I also found some sources that didn't really work.
* Some subreddits are fun jokes themselves, but aren't very funny in the context of a PowerPoint, like [/r/fifthworldproblems](https://www.reddit.com/r/fifthworldproblems)

#### That's a lot of API requests
Yes it is, and that's why I sat down and actually learned Javascript promises and async/await for this one. I'm pleased to report its speed is _reasonable_ and its performance only degrades _very badly_ if Reddit or Wikipedia go down. I would like to fix this.

#### More detailed procedural generation
I watched a few GDC talks about procedural generation and found out about a program called [Tracery](http://tracery.io/) used to generate grammars for procedural generation. Using this and a dataset called [Corpora](https://github.com/dariusk/corpora) that is lists of different types of words, I have started to [rework some of the text generation](https://github.com/pricecomstock/pptxgen/blob/master/slidegen/sources/grammar/grammar.js), especially on the "about me" slide. One example I'm proud of is the "nickname" bullet type, which results in statements like 
* given the nickname "The Mayoress of Textiles"

Here's the relevant high-level portions of the grammar.

{% highlight javascript %}
{
  nickname: [
    "#honorific# #field.capitalizeAll#",
    "#honorific# #mood.capitalizeAll#",
    "#honorific# #iabCategory#"
  ],
  nicknameStatement: [
    'you can call me "#nickname#"',
    'I\'ve been called "#nickname#"',
    'given the nickname "#nickname#"'
  ],
  aboutMe: [
    "#expertStatement#",
    "#qualification#",
    "#funFact#",
    "#origin#",
    "#nicknameStatement#"
  ]
}
{% endhighlight %}

#### The Future
The grammar-based generation is generally better than the Reddit and Wikipedia generation, so I would like to implement more of it. I think for slides less specialized than the "About Me" slide, a mix of both will be good.


## TL;DR
PowerPoint Karaoke is fun but also sort of sucks so I tried to increase the fun-to-effort ratio by creating my own generator.

#### Pros
* A brand new, unique presentation is generated within seconds
* The machine doesn't have any subjective take on what a presenter will do with the slides, which I think leads to more variety and more of a challenge to justify
* No need to deal with any software or files other than a web browser

#### Cons
* Sometimes Reddit goes down
* When the presenters aren't experienced improvisers, sometimes the chaotic slides and fewer teed-up jokes are more of a problem, so this might be worse for team building
* harder to guarantee safe-for-work content
* internet connectivity required
* slide layouts are not pixel-perfect or really even fairly-consistent from device to device