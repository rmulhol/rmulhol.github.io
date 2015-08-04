---
layout: post
title:  "Doing More Stuff More Easily: Implementing the Open-Closed Principle in Server Architecture"
date:   2015-08-04
categories: Java
tags: server router
--- 

As you may have deduced from [several][prev_post1] [previous][prev_post2] [posts][prev_post3], I've been spending some of my time working on an HTTP server. It's raised some really interesting challenges on numerous fronts, but one that really got me thinking had to do with figuring out the proper mechanism for routing requests.

It didn't seem like a particularly big deal when I only had to deal with a few simple requests, since I could just figure everything out with a conditional:

{% highlight java %}
if (request.getMethod().equals("GET") && request.getUri().equals("/")) {
    return new RootResponse();
} else if (request.getMethod().equals("GET") && request.getUri().equals("/file")) {
  return new FileResponse();
} else {
  return new FourOhFourResponse();
}
{% endhighlight %}

This seems to work OK with a small amount of relatively simple requests, but you're probably already starting to notice the issue: what about when things get more complicated? Say I've got to deal with 100 different requests, sometimes checking things in the header (`request.isAuthorized()`, `request.hasRange()`, etc.). That conditional is going to get pretty nasty.

And, conveniently, this problem even has a name: it's an illustration of what happens when you violate the Open-Closed Principle. That is to say, this approach is not open for extension but closed for modification. Every new request is going to require that you go in an edit that conditional.

But is there another way? Perhaps server architects have just been toiling away in this sort of mess for years, carrying out such a herculean task that we ought stand in awe of their dedication and resilience.

Of course there's another way. Anyone who's got a passing familiarity with Rails should know as much. Does this look familiar?

{% highlight ruby %}
Rails.application.routes.draw do
  root                  "static_pages#home"

  get     "help"    =>  "static_pages#help"
  get     "about"   =>  "static_pages#about"
  get     "contact" =>  "static_pages#contact"

  ...
end
{% endhighlight %}

That's the beginning of the `config/routes.rb` file from Michael Hartl's [Rails Tutorial][rails_tutorial], and it's accomplishing precisely the task we've set out to tackle. It's also pretty straightforward and elegant - `x` request maps to `y` controller action.

That's a pretty important verb: "map". It's also a data structure. In Java, we might think about a `HashMap`, where keys could be requests corresponding to values that designate appropriate controllers.

In retrospect, it's got this sort of facepalm-eliciting simplicity: associate routes with controllers in a map, then make calls to that map whenever you need to route a request.

{% highlight java %}
public class Routes {

    HashMap<String, Controller> routes;

    Routes() {
      this.routes = new HashMap<String, Controller>();
    }

    public void addRoute(String request, Controller controller) {
      this.routes.put(request, controller);
    }

    public Controller getRoute(String request) {
      return this.routes.get(request);
    }
}
{% endhighlight %}

Of course, this is a fairly naive implementation that could be finessed a bit, but it's the seedling for an extensible architecture that satisfies the Open-Closed principle, and it makes your life a lot easier when you want to add new routes to your server down the line.

[prev_post1]: {% post_url 2015-06-06-simple-java-server %}
[prev_post2]: {% post_url 2015-06-17-handler-adapter-architecture %}
[prev_post3]: {% post_url 2015-06-19-java-logging-utility %}
[rails_tutorial]: https://www.railstutorial.org/