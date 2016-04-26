---
layout: post
title:  "Setting up Jekyll on Windows"
date:   2016-04-22 08:45:00 +0100
categories: jekyll
---

Okay, following on from [my last post]({% post_url 2016-04-20-screw-java-lets-use-jekyll %}), let's talk about throwing together a working website in Jekyll from beginning to end. The website will include vanilla Bootstrap 3 styling, Disqus comments for your blog, a generated sitemap.xml and will be hosted on Github Pages. This post covers getting the base install up and running on a Windows system and deploying the site.

First things first: you'll need a Github account. There are other ways of setting up a low cost Jekyll install ([Amazon S3](https://aws.amazon.com/s3/) springs to mind) but the Github approach is deliciously straightforward. Anyway: get yourself a [Github](https://github.com) account. While you're away, go grab [Git](https://git-scm.com/) if you don't have it already. You'll be using it to create a development copy of your website and pushing changes to the live server. Git's a version control system and the nature of it is a discussion for another day. For the purposes of what we're doing here, it's sort of like what folks have to do with FTP and website deployment with less swearing, sweating and finger-crossing. So: sign up to Github. Grab Git. It'll only take a couple of minutes. I'll be here when you get back.

**[LIFT MUSIC]**

On Github you'll need to create a repository and the repository *must* match your account name exactly. For example, my repository is called jayb0b.github.io. You'll then need to throw open a command prompt, navigate to wherever you want to keep your development copy and do the following:

```
git clone https://github.com/your-gibhub-username/your-gibhub-username.github.io
```

You'll now have an empty folder called 'your-gibhub-username.github.io'. Time to get Jekyll up and running.

## A Russian nesting doll situation

Under the hood, Jekyll uses Ruby and it's package management system gem. This was always a bit of a faff you get working under Windows, but thankfully there's an easy solution for it now: another package manager. [Chocolatey](https://chocolatey.org/) is a Windows equivalent of brew and apt-get: to install it, copy and paste the following into a cmd prompt *that has admin permissions*:

```
 @powershell -NoProfile -ExecutionPolicy Bypass -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
```
Close the command prompt then reopen a new one, again with admin permissions.

```
choco install ruby -y
```
Close and reopen, again with admin permissions.

```
gem install jekyll
```
Close and reopen, then new one doesn't need admin permissions.

```
jekyll new C:\path\to\the\empty folder\created by\git clone\
cd C:\path\to\the\empty folder\created by\git clone\
jekyll s
```

Done! You now have a server, running on port 4000. To publish the default website on Github, you'll need another command prompt. Navigate to the working folder and do this:

```
git add --all
git commit -m "first commit"
git push origin master
```
You'll be asked for your username and password for Github. A couple of seconds later, if you go to http://your-gibhub-username.github.io the standard Jekyll template will appear. Moving forward, this will be how you update the styling on the live site and also how you publish content.

In the [next post]({% post_url 2016-04-25-jekyll-and-bootstrap-3 %}), I'll start exploring the Jekyll folder structure and **completely** break the styling on the site by installing Bootstrap.
