---
layout: post
title:  "Jetty and Spring Boot: SEO and URL Rewriting"
date:   2016-10-21 14:45:00 +0100
categories: java spring jetty 
---
One thing that the Java ecosystem is still pretty bad at in 2016 is getting SEO right. The standard response is that it doesn't need to do this: just stick an Apache web server on port 80 in front of Tomcat and boom! You've got access to all of Apache's functionality like mod_rewrite. But this approach gets a spanner thrown in the works when you apply modern Java web application practices into the mix. How do you serve static content via Apache if you're deploying your application as a fat jar? I haven't found a good answer for this, so I started digging. And digging. And by the time I could feel the heat of the earth's core I had something resembling a solution.

Although Spring Boot uses Tomcat out of the box, you can switch to Jetty or Undertow. It looked like Jetty had the rewrite module with the best [documentation](https://www.eclipse.org/jetty/documentation/9.3.x/rewrite-handler.html), so I dived into the code. Replacing Tomcat with Jetty [is pretty well documented](http://docs.spring.io/spring-boot/docs/current/reference/html/howto-embedded-servlet-containers.html) but the rewrite engine isn't included. Add it into your pom using [one of these versions](https://mvnrepository.com/artifact/org.eclipse.jetty/jetty-rewrite): make sure the one you grab matches the version of Jetty you're running.

``` java
  Server server = new Server();

  RewriteHandler rewrite = new RewriteHandler();
  rewrite.setRewriteRequestURI(true);
  rewrite.setRewritePathInfo(false);
  rewrite.originalPathAttribute("requestedPath");

  RedirectPatternRule redirect = new RedirectPatternRule();
  redirect.setPattern("/redirect/*");
  redirect.setReplacement("/redirected");
  rewrite.addRule(redirect);

  RewritePatternRule oldToNew = new RewritePatternRule();
  oldToNew.setPattern("/some/old/context");
  oldToNew.setReplacement("/some/new/context");
  rewrite.addRule(oldToNew);

  RewriteRegexRule reverse = new RewriteRegexRule();
  reverse.setRegex("/reverse/([^/]*)/(.*)");
  reverse.setReplacement("/reverse/$2/$1");
  rewrite.addRule(reverse);

  server.setHandler(rewrite);
```

Looks fine, right? You just need to get a handle on the Server object and whack some rules in. This is true. Kind of. It doesn't help that the above code *from Jetty's website* gets several method names wrong, but if you follow this example, you'll end up with a a busted servlet context when you spin up the app.

Here's an example of a working Spring Boot configuration.

``` java
@Component
public class JettyConfiguration {
	@Bean
	public EmbeddedServletContainerCustomizer servletContainerCustomizer() {
	    return new EmbeddedServletContainerCustomizer() {

	        @Override
	        public void customize(ConfigurableEmbeddedServletContainer container) {
	            if (container instanceof JettyEmbeddedServletContainerFactory) {
	                configureJetty((JettyEmbeddedServletContainerFactory) container);
	            }
	        }

	        private void configureJetty(JettyEmbeddedServletContainerFactory jettyFactory) {
	            jettyFactory.addServerCustomizers(new JettyServerCustomizer() {

	                @Override
	                public void customize(Server server) {
	                    RewriteHandler rewrite = new RewriteHandler();
	                    rewrite.setRewriteRequestURI(true);
	                    rewrite.setRewritePathInfo(false);
	                    rewrite.setOriginalPathAttribute("requestedPath");
	                    RedirectPatternRule redirect = new RedirectPatternRule();
	                    redirect.setPattern("/redirect/*");
	                    redirect.setLocation("/redirected/");  
	                    rewrite.addRule(redirect);
	                    HandlerCollection handlerCollection = new HandlerCollection();
	                    handlerCollection.setHandlers(new Handler[] {rewrite,server.getHandler()});
	                    server.setHandler(handlerCollection);
	                    
	                }
	            });
	        }
	    };
	}
}
```

Most of this class is boiler plate stuff so you can get to the server object and add new functionality. The key thing is this section:

``` java
HandlerCollection handlerCollection = new HandlerCollection();
handlerCollection.setHandlers(new Handler[] {rewrite,server.getHandler()});
server.setHandler(handlerCollection);
```

If you use server.setHandler(rewrite); you will end up removing the Servlet Context entirely, so you need to pass back in a HandlerCollection *that includes the default handler*. It looks like the handlers chain in order so if you pass in {server.getHandler(), rewrite} the rewrite rule above will not fire.

However, if you pass in {rewrite, server.getHandler()}, any requests to yourwebsite.com/redirect/ will 302 redirect to /redirected/. 









