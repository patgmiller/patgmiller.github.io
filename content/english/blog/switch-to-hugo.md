---
date: '2024-01-19T22:33:21-07:00'
author: Patrick Miller
draft: false
image: "/images/blog/2024/hugo-logo-wide.svg"
summary: Over the years, there have been numerous apps and libs for managing one's own website. Collectively these can be referred to as content management systems (CMS) and the majority started out as a combination of some programming language paired with a storage backend (database).
title: 'Switch to Hugo'
categories:
- Programming
tags:
- cms
- go
- hugo
- jamstack
---
## Background

Over the years, there have been numerous apps and libs for managing one's own website. Collectively these can be referred to as content management systems (CMS) and the majority started out as a combination of some programming language paired with a storage backend (database). Some of the more commonly known open source examples might be Django<sup>[[1]](#references)</sup>, Flask<sup>[[2]](#references)</sup>, Ruby on Rails<sup>[[6]](#references)</sup>, Wordpress<sup>[[3]](#references)</sup>, Drupal<sup>[[4]](#references)</sup>, Joomla<sup>[[5]](#references)</sup>, and there are many many more! Most of these appeared on the scene in early 2000's. Not long after we saw the emergence of "static" site generators<sup>[[7]](#references)</sup>, which later Mathais Biilmann, CEO of Netlify termed this approach as JAM Stack<sup>[[8]](#references)</sup>, Javascript, APIs and Markup. There are a couple of different ones out there, Jekyll<sup>[[9]](#references)</sup>, Ghost<sup>[[10]](#references)</sup>, and Hugo<sup>[[11]](#references)</sup> to name a few.

## From Jekyll To Hugo

A few years ago I switched to using Jekyll with github.com for my blog, mainly because I was tired of paying for server and database hosting. However, I never cared very much for Ruby, mostly for personal reasons which are not very objective or quantifiable:

- I started with Python so trying to code in Ruby just felt ... off to me.
- The Ruby community always felt smaller than other languages.

Not that I consider Ruby to be inferior, because I've known several talented Ruby developers and successful projects built in Ruby, it's just my personal preference. Also not because I couldn't hack it, because I spent the last 5 years working as a Site Reliability Engineer where I wrote Chef Recipes (Ruby) to manage some of the custom provisioning for Google Cloud and internally hosted virtual machines. As a result I've been intending to switch over to Hugo for a while. Since I recently have some free time, and the motivation to be publishing content, so here we are let's dive in!

### Theme / Site Template

Choosing a template for the new site is where we begin. Initially there were only two requirements I considered, but as I began to search, necessity added a third.

1. Must be responsive!
1. Dark theme option, preferably with a toggle.
1. Demo, can I easily try it out?

I remember when mobile responsive sites were optional, I even remember building a site using tables <i class="fa-solid fa-face-grin-squint-tears"></i>, but nowadays you cannot even have a respectable site without it. Having a light/dark theme option is a little more recent and being a bit of a night owl means I appreciate not being blinded, or waking my wife who is trying to sleep next to me, with a portable noon day sun on a site that is largely white and bright in my browser. As our search continues, we quickly find ourselves heading over to [themes.gohugo.io](https://themes.gohugo.io/), where there are a host of options. And as several options are presented the need to take one or more candidates out for a test drive becomes apparent. I don't know about you, but when I demo something, what I really want is to actually play with it, look under the hood, and see how it works before I come to any final conclusions. Maybe I'm just more curious than most, or perhaps I just take too long and get stuck struggling with analysis paralysis, but this step probably took me the longest!

Eventually, we realize there are additional considerations we want or need ...

- Like what, features, plugins, or extensions does hugo have and does the template already incorporate them?
  - Which are them missing? And how badly do I need them?
- [github.com/gethugothemes/hugo-modules](https://github.com/gethugothemes/hugo-modules) have built several nice modules
- Most important, *where can I get Emoji's* for my website please! <i class="fa-regular fa-face-sad-cry"></i>
  - ... and of course in my case they now also should probably play nice with a light / dark theme

- Emoji's please!
  - fontawesome.com icons

### Content Migration

`tl;dr`

{{< highlight shell >}}
$> hugo convert
{{< / highlight >}}

I tried a couple migration scripts out there, one or two gists on github.com but nothing worked as well as `hugo convert` and afterwards a little manual clean up. I won't get into the migration details since it doesn't interest me at all.

### Bug Fix

Found and fixed bug in the [search hugo-module](https://github.com/gethugothemes/hugo-modules/pull/35). It's always fun and rewarding to contribute to the OpenSource community, especially when it is accepted <i class="fa-solid fa-smile-beam"></i>

## Conclusion

Overall I enjoyed converting over to `hugo`. I will say for most there is a learning curve, which largely depends on how well you know golang, I didn't find it to be that hard. Based on what little I've done with rolling my own site I'd recommend one pick one that fits your skill sets, because unless it's your full time business I personally don't want to spend tons of extra time fighting to make it work. Unless of course you're looking to try something new, however in the end I will often say do what works for you!

## References

1. [Django (web framework)](https://en.wikipedia.org/wiki/Django_(web_framework))
1. [Flask (web framework)](https://en.wikipedia.org/wiki/Flask_(web_framework))
1. [Wordpress](https://en.wikipedia.org/wiki/WordPress)
1. [Drupal](https://en.wikipedia.org/wiki/Drupal)
1. [Joomla](https://en.wikipedia.org/wiki/Joomla)
1. [Ruby on Rails](https://en.wikipedia.org/wiki/Ruby_on_Rails)
1. [Static site generator](https://en.wikipedia.org/wiki/Static_site_generator)
1. [JAMStack](https://jamstack.org/)
1. [Jekyll (software)](https://en.wikipedia.org/wiki/Jekyll_(software))
1. [Ghost (blogging platform)](https://en.wikipedia.org/wiki/Ghost_(blogging_platform))
1. [Hugo (software)](https://en.wikipedia.org/wiki/Hugo_(software))
