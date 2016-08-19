---
layout: post
title:  "Adventures in JBoss Seam pt 1"
date:   2016-08-19 10:45:00 +0100
categories: java javaEE 
---

Java web development has come a long way in the last five years. We used to be drowning in configuration minutia and boilerplate code, now you can have a barebones but functional application in the time it take to make a cup of coffee. Other languages have been here for a long time though, and the growing pains we've experienced to get here from the more "enterprise-y" shackles that are a part of Java's history have led us down some interesting blind alleys. In this multi part article I'm going to discuss Jboss Seam and my experiences with it back in space year 2010.  In the film, Helen Mirren and Roy Scheider were venturing out to Jupiter. In my career, my abiding memory of the year was many weekends in the office trying to launch a product that had no chance of launching anywhere near the target release date.

We'd recently acquired a project manager to help us get through the development of the product: the biggest and most complex thing our small IT team had tackled. The project manager was keen on developing it in Java and so was I: I'd been cutting my teeth on a Coldfusion website and could see that, as a technology, it was going nowhere. I wanted to dive into the big boy stuff. To get things moving, the PM and I started researching web frameworks. Spring was fairly mature at that point but was still perceived as an XML-based nightmare. There were (and still are) a host of alternatives. [Tapestry](tapestry.apache.org/). [Wicket](wicket.apache.org/). Even [Grails](https://grails.org/) was kicking about back then. However, we looked at Jboss Seam and decided it was the right choice for the project.

On paper, Seam looked like a great match for our requirements. I experimented with a few test apps, devoured a couple of books on the subject and felt I was ready. I'd created a few Rails projects, Seam looked similar? Kind of? Let's have a look at what the [Seam website](http://seamframework.org/) says about the platform:

> "Seam is a powerful open source development platform for building rich Internet applications in Java. Seam integrates technologies such as Asynchronous JavaScript and XML (AJAX), JavaServer Faces (JSF), Java Persistence (JPA), Enterprise Java Beans (EJB 3.0) and Business Process Management (BPM) into a unified full-stack solution, complete with sophisticated tooling."

What I didn't appreciate at the time was that I hadn't set myself up for learning a new technology: I'd set myself up for learning several pretty complex things simultaneously in a very short timeframe. But I'm getting ahead of myself.

Seam was an interesting framework. As it's name suggested it was glue that made three other frameworks play together nicely. You could map out your ORM using JPA and Hibernate, create EJBs that contained business logic and expose the data through the EJBs directly to web pages containing JSF widgets. Whereas the core of Spring revolves around the concept of dependecy injection, Seam introduced a concept called dependecy outjection which, together with DI, meant that the framework exhibited something that the developers called "bijection". 

Bijection.

Outjection and bijection might be the most ridiculous pieces of it terminology I've come across in a decade in the industry. The latter sounds more like the name of a specialist porn site than a product feature to me.   

Anyway, the code would look something like this:

```
@Stateful
@Name("myShoppingCart")
public class ShoppingCart {

    @In
    User user;

    @Out
    List<Product> cartContent;

    \\ methods relating to shopping cart business logic 
}
```

If you've used Spring before, some of this should look sort of familiar. @Stateful is functionally similar to the @Service and @Component et al stereotype annotations: you're identifying the class so that it ends up in the EJB container. @In serves the same function as @Autowired: you're injecting a scoped bean. @Out is the real witchcraft: you don't need to marshall cartContent into json, you don't need to mash together templates and maps: you simply reference the variable in a JSF component on the page and it binds the page to the server side gubbins. Changes you make in the widget reflect immediately in the state of the EJB. Bijection!

Seam also came with a Rails-style scaffold utility called seam-gen. Set a few options, point it at your database and boom! A working project with CRUD functionality. The path ahead was clear. Scaffold a UI out of our existing database and start plugging in custom code to handle the trickier requirements.

Wrong, wrong, wrong, wrong. 