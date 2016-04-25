---
layout: post
title:  "Jekyll and Bootstrap 3"
date:   2016-04-25 012:00:00 +0100
categories: jekyll
---

Okay, [last time]("{% post_url 2016-04-22-jekyll-setup-on-windows %}") we finished up with a working Jekyll install and published the vanilla website over on Github pages. At the end of this post, we'll end up with a website that's far worse looking but will provide a decent foundation for moving forward. We'll be installing Bootstrap 3.

## A jaunt through the file system

Before we get there, let's explore around the file structure of Jekyll. There's a fair amount going on here, but it's all pretty straightforward.

![Jekyll folder structure](/img/jekyll-folder-structure.png)

* **_includes**: Snippets of HTML that you can reuse across your site. A good place to throw things like headers, footers and so on.
* **_layouts**: The base templates of your website. If you look at default.html, you'll see elements from the \_includes folder being used. There's also an element called \{\{content\}\} which we'll get to in a sec.
* **_posts**: This is where your blog entries go. You create them as Markdown files.
* **_sass**: Sass files that generate CSS.
* **css**: This has another sass file in it that references the \_sass folder.
* **config.yml**: A YAML file that contains the base settings for your website.
* **about.md**: This is an example of an 'About' page provided by Jekyll. It's in Markdown format.
* **feed.xml**: An RSS feed for your blog posts. There shouldn't be much of a need to do anything with this. It Just Works.
* **index.html**: Your homepage.

If you've already generated your site, you'll also have a \_site folder, which is where the generated HTML goes. There should be a sample file in the \_posts folder. If you look at this, you'll see it has at the top of the file:

```
---
layout: post
title:  "Welcome to Jekyll!"
date:   2016-04-25 08:54:01 +0100
categories: jekyll update
---
```
Jekyll parses this metadata when building the site. So the line 'layout:post' means that, when generated, the markdown here will be converted to HTML and will replace the \{\{content\}\} element in post.html (in the \_layouts folder). If you look at post.html, you'll see that it *also* has a layout:

```
---
layout: default
---
```
This works the same way. The \{\{content\}\} element in default.html will be populated with the content of post.html. Which, as we have established, gets the generated content of the markdown file.

## Let's nuke the styling from orbit. It's the only way to be sure

First up, it's probably a good idea to get the local Jekyll server up and running. Go to the project folder and type:

```
jekyll s
```

You can now access the site on localhost:4000. Any changes you make (apart from changes to config.yml) will automatically pop into place without you needing to restart stuff. Onto Bootstrap!

Bootstrap uses LESS by default as a CSS processor, but there's an official SASS version too. You can grab a zip of it [here](https://github.com/twbs/bootstrap-sass). In the zip file, the files you need are in /assets/stylesheets/. Move the bootstrap folder and bootstrap.scss to the \_sass folder in Jekyll. Next up, you'll need to include the bootstrap files in CSS generation and unhook the existing ones. Go to /css/main.scss and replace it with this:

```
---
---

@charset "utf-8";

// Import partials from `sass_dir` (defaults to `_sass`)
@import
        "bootstrap"
;
```

You can now safely delete the other files in the  \_sass folder. And if you go to localhost:4000... *OH MY GOD MY VISION!* Yep, we've completely screwed the site styling. Which is *fine*. You'll need to start dipping into the \_includes and \_layouts folders and start plugging in Bootstrap 3 class names and restructuring the HTML to something more in line with what you want. You probably want the Bootstrap javascript file and JQuery on the site too, and for that I'd suggest using a CDN rather than a local file.  I use [cdnjs](https://cdnjs.com/). *Just make sure the Bootstrap JavaScript matches the version you downloaded earlier*. Inside footer.html, stick the following:

```
<script src = "https://cdnjs.cloudflare.com/ajax/libs/jquery/2.2.3/jquery.min.js"></script>
<script src = "https://cdnjs.cloudflare.com/ajax/libs/twitter-bootstrap/3.3.6/js/bootstrap.min.js"></script>
```

That's enough for one post: next up, making a sitemap.
