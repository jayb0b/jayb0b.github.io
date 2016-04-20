---
layout: post
title:  "Screw Java, let's use Jekyll"
date:   2016-04-20 08:45:00 +0100
categories: jekyll java
image: jekyll.jpg
---

I can be a procrastinator of epic proportions. I love dipping my toes in new technologies. So, when it came to creating my own blog without a set deadline in mind, I resorted to dithering around and creating a cavalcade of half finished prototypes that are currently strewn across my Bitbucket account.

> "Python's pretty cool, maybe I can use Flask and GAE..."

A month or two later...

> "Oh, hey, Openshift's got a free hosting option, maybe I could knock something together using Guice, RESTful services and Angular..."

On and on this went. An endless cycle of [yak shaving](https://en.wiktionary.org/wiki/yak_shaving) with a bunch of half baked code to show for it. Coding is fun. Learning new stuff is fun. But sometimes you just need to get things done. Simply, quickly, efficiently. So I settled on a path which threw out the window everything I've learned about creating dynamic websites over the last decade and built a site using [Jekyll](https://jekyllrb.com/). A day later, I'm writing my first post. Because, aside from tidying up the styling, the site is done and it works.

Jekyll, at first glance, seems like an oddball approach. There's no server-side language and no database involved. [Liquid](https://github.com/Shopify/liquid/wiki) is used as a templating engine and you create blog posts as Markdown files and stuff them into a folder. When you edit content and hit build an *entire* static HTML site is generated. It's then a simple case of punting across the static HTML to your host and that's it. Job done.

There are some interesting wrinkles that make this far more appetising than it sounds. Firstly Jekyll's command line executable is pretty nifty. You can scaffold a new site and get it running in a couple of commands. There's a development server that watches for changes meaning that building your site works pretty much the same as any other development workflow.

The killer feature, however, might be integration with [Github pages](https://pages.github.com/). If you have a Github account, you can host a Jekyll site there free of charge. It's a simple case of creating a repo with a specific name, cloning it, throwing a Jekyll install into the repo and pushing it back up.

There are some provisos, however. When your site has no backend, certain features require... more creative approaches. Forms for example. You can't POST data and process it the way you would on a standard dynamic website. There's a bunch of services that are attempting to fill this gap such as [FormKeep](https://formkeep.com/). But, for my money, $9 a month is a bit of an ask for a single web form. If you need functionality like this I'd suggest you're reaching the point where Jekyll isn't the best fit anymore. E-Commerce looks doable though: [Snipcart](https://snipcart.com/) has some useful tutorials on integrating their services with Jekyll. They'll skim 2% off your sales for use of their services, however.

So, in conclusion: the right tool for the right job. Keep it simple. Find the approach for a project with the least friction. My next post will cover the process end to end, including getting Bootstrap and Disqus into the mix and faffing around with custom variables.
