---
layout: post
title:  making a better "about me" slide for powerpoint generator
date:   2019-04-20 12:00:00
description: using grammars and tracery.io for more specific generation
tags: [pptxgen]
---

I watched a few [GDC talks about procedural generation](https://www.youtube.com/watch?v=WumyfLEa6bU) and become especially interested in grammar-based procedural generation. Grammar-based generation uses templates to fill in placeholders with randomly chosen content, and is recursive, so that content can itself be another template with placeholders. That's my best shot at defining it, and you can either try to decipher that or just look at the examples below which i think should make things pretty clear.

### Procedural Text Generation for Powerpoint Generator
I'm using two main resources to create strings for [PowerPoint Generator](https://pptx.pc.codes/). First, there is a wonderful program by Kate Compton called [Tracery](http://tracery.io/). Tracery is used to generate and run text-based grammars, although it can also be used to generate HTML and other markup languages that go beyond just simple text. I also found a dataset called [Corpora](https://github.com/dariusk/corpora) that is simply a large (and CC0) collection of categorized word and phrase lists.

You can see the full version [here](https://github.com/pricecomstock/pptxgen/blob/master/slidegen/sources/grammar/grammar.js), but I'll go into detail on a few sections below, which are all called by this top-level template.

{% highlight javascript %}
{
  aboutMe: [
    "#expertStatement#",
    "#qualification#",
    "#funFact#",
    "#origin#",
    "#nicknameStatement#"
  ]
}
{% endhighlight %}

### Nicknames
One example I'm proud of is the "nickname" bullet type, which results in statements like 
* given the nickname "The Mayoress of Textiles"
* you can call me "Reverend Sister Scuba Diving"
* given the nickname "Lieutenant Tame"
* you can call me "District Attorney Post-Conceptualism"
* I've been called "Lord Lieutenant Dr. Road-Side Assistance"
* I've been called "Princess Geography"
* given the nickname "Father Resourceful"

Here's the relevant high-level portions of the grammar:
{% highlight javascript %}
{
  field: ["#iabCategory#", "#industry#", "#academicSubject#", "#artType#"],
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
}
{% endhighlight %}

### Qualifications
These are statements that present some sort of "expertise." These are not as immediately funny as the nicknames but often give the speaker absurd credentials unrelated to the topic to justify and speak about. I like the expertStatement template enough that I made it one of the top-level about me options.
* 33 years of experience in abstract expressionism
* Certified in new media art
* Bridge And Lock Tender for Mayo Clinic and Dow Chemical
* Associate's degree in Personal Finance
* 0 years of experience in Law Enforcement
* Expert in Sexuality
* Bachelor's degree in Car Culture
* Weaving Machine Setter for The Walt Disney Company and Catholic Relief Services
Note that this one uses the same "Field" as above. It's a pretty versatile word set for this slide.

{% highlight javascript %}
{
  organization: [
    "#charity#",
    "#company#",
    "#company# and #charity#",
    "#company# and #company#",
    "#charity# and #company#",
    "Big #industry.capitalizeAll#",
    "#usGovAgency#",
    "#newspaper#"
  ],
  expertPreposition: [
    "expert in",
    "master of",
    "Ph.D. in",
    "master's degree in",
    "bachelor's degree in",
    "associate's degree in",
    "certified in",
    "enjoys",
    "#characterType# of"
  ],
  expertStatement: ["#expertPreposition.capitalize# #field#"],
  qualification: [
    "#occupation.capitalizeAll# for #organization#",
    "#expertStatement#",
    "#integer35# years of experience in #field#"
  ]
}
{% endhighlight %}

### Fun Facts
This template is shallower than the others and the statements are more directly written by me with a word or two substituted by the templates. I'd like to add more variety to this one as I come up with more options. A lot of times, I see a word list in Corpora and write the statement around it!
* spent a lot of time in my basement
* befriended an ox named Mandy
* always wearing at least one item made of kevlar
* competitive in minor league Sledge hockey
* survived an ultraviolet stellar radiation
* really fuckin blitzed as we speak
* spent a lot of time in my aircraft cabin
* most prized possession is a tweezers

{% highlight javascript %}
{
  opinionVerb: [
    "likes",
    "doesn't like",
    "doesn't care for",
    "loves",
    "feels weird about",
    "needs",
    "I don't understand",
    "I don't see the need for",
    "really enjoys"
  ],
  opinionTarget: [
    "#field#",
    "#artType#",
    "#occupation.s#",
    "#charity#",
    "#eventType#",
    "#appliance.s#",
  ],
  opinionPhrase: [
    "#opinionVerb# #opinionTarget#",
    "#opinionTarget# makes me #mood#"
  ],
  funFact: [
    "has a pet #petAnimal# named #dogName#",
    "befriended #animal.a# named #dogName#",
    "drives #car.a#",
    "#opinionPhrase#",
    "survived #environmentalHazard.a#",
    "#nicknameStatement#",
    "is capable of performing #fightingMove.a#",
    "most prized possession is #object.a#",
    "spent a lot of time in my #room#",
    "grew up in a #naturalMaterial# hut",
    "always wearing at least one item made of #technicalFabric#",
    "#severityModifier# #stateOfInebriation# #currentTimeModifier#",
    "#sportsTeam# fan",
    "competitive in #sportLevel# #sport#"
  ],
}
{% endhighlight %}

### Origin
This one honestly isn't that exciting, but it adds some flavor while being less overwhelming to justify than most of the other options.
* originally from Iceland
* lived in Guatemala
* grew up in Burlington, North Carolina
* grew up in Seychelles
* originally from Elizabeth, New Jersey
* lived in Plainfield, Illinois
* originally from Ecuador

{% highlight javascript %}
{
  originVerb: ["originally from", "grew up in", "lived in"],
  origin: ["#originVerb# #city#", "#originVerb# #country#"],
}
{% endhighlight %}

## The whole slide
Finally, here are a few examples of completed slides:
<img class="fullimg" src="/img/2019/pptx-slide-samples/2019-04-20-131401_988x552_scrot.png">
<img class="fullimg" src="/img/2019/pptx-slide-samples/2019-04-20-131448_985x552_scrot.png">
<img class="fullimg" src="/img/2019/pptx-slide-samples/2019-04-20-131516_986x548_scrot.png">
<img class="fullimg" src="/img/2019/pptx-slide-samples/2019-04-20-131655_988x553_scrot.png">
<img class="fullimg" src="/img/2019/pptx-slide-samples/2019-04-20-131741_986x551_scrot.png">
<img class="fullimg" src="/img/2019/pptx-slide-samples/2019-04-20-131916_981x550_scrot.png">
<img class="fullimg" src="/img/2019/pptx-slide-samples/2019-04-20-132008_985x550_scrot.png">
<img class="fullimg" src="/img/2019/pptx-slide-samples/2019-04-20-132055_990x551_scrot.png">

## Go try it!
You can generate your own slideshows at [forgotmyslides.xyz](https://forgotmyslides.xyz)