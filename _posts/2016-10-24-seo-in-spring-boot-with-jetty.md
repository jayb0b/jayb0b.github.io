---
layout: post
title:  "SEO in Sping Boot with Jetty"
date:   2016-10-24 13:20:00 +0100
categories: java spring jetty 
---
Continuing on from my [last post](/java/spring/jetty/2016/10/21/url-rewriting-in-spring-boot-and-jetty.html), lets set up a couple of bits of good practice that will minimise the amount of duplication that web crawlers like googlebot will run into on your site. One common issue is not getting a good handle on the canonical domain name for a website. You definitely don't want to have example1.com and example2.com both pointing to the same content as google will penalise you heavily for duplication. However, if at all possible, I would suggest avoiding subdomains pointing at the same content (www.example.com and example.com, for example). This is straightforward to acheive using the same implementation of JettyServerCustomizer that we used in the previous example. I'm hardcoding the example domain in here, but you can also @Autowire in Environment and set it up as a property.

``` java
@Override
public void customize(Server server) {
	String[] virtualHosts = new String[] {"example.com"}
	MovedContextHandler movedContextHandler = new MovedContextHandler();
	movedContextHandler.setContextPath("/");
	movedContextHandler.setNewContextURL("http://www.example.com");
	movedContextHandler.setPermanent(true);
	movedContextHandler.setDiscardPathInfo(false);
	movedContextHandler.setDiscardQuery(false);
	movedContextHandler.setVirtualHosts(virtualHosts);
	HandlerCollection handlerCollection = new HandlerCollection();
	handlerCollection.setHandlers(new Handler[] {movedContextHandler,server.getHandler()});
	server.setHandler(handlerCollection);
}
```

So in this example, for the defined virtual hosts a permenant (301) redirect is created to http://www.example.com and the query and path data are retained. 

The next thing that's worth fixing in pretty much all circumtances is consistent trailing slashes in urls. Ideally, you want www.example.com/foo and www.example.com/foo/ to point at the same page. The problem is that crawlers consider these two urls to be different page and once again you're looking at getting dinged for duplicate content. There's a redirect handler (see [previous post](/java/spring/jetty/2016/10/21/url-rewriting-in-spring-boot-and-jetty.html)) that let's you use regex and with its help you can add in a trailing slash automatically everywhere.    

``` java
@Override
public void customize(Server server) {
	//Snipped out the above movedContextHandler code.
 	RewriteHandler rewrite = new RewriteHandler();
	rewrite.setRewriteRequestURI(true);
	rewrite.setRewritePathInfo(false);
	rewrite.setOriginalPathAttribute("requestedPath");
	RedirectRegexRule redirect = new RedirectRegexRule();
	//The regex below matches against any urls that don't end in a slash.
	redirect.setRegex("((.*)[^/]$)");
	// $1 represents the match found above. Append a slash to it.
	redirect.setReplacement("$1/");
	rewrite.addRule(redirect);
	handlerCollection.setHandlers(new Handler[] {rewrite,movedContextHandler,server.getHandler()});
	server.setHandler(handlerCollection);
}
```


