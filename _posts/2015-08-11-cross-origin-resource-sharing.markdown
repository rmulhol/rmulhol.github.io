---
layout: post
title:  "Working With APIs: On Cross-Origin Resource Sharing"
date:   2015-08-11
categories: javascript
tags: ajax cors
--- 

If you're working with an API, one thing that you might want to do is use [AJAX][ajax] to make requests from the browser. Unfortunately, unless the API provides the required response headers, you might run into some hiccups along the way.

If you fire a request from the browser at an API that doesn't support [Cross-Origin Resource Sharing (CORS)][cors], you'll get something resembling the following error message in the console:

> XMLHttpRequest cannot load https://api.you.want.to.hit.com/get_data. No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'http://localhost:8080' is therefore not allowed access.

So, what's going on here? And how do we solve the problem?

**What is CORS?**

The reason you're seeing this error message is that you're trying to launch a cross-domain request from the browser. That is, you're asking the browser to make a request to a URL with a different protocol, host, and/or port number from that of the originating request. This violates [the same-origin policy][sop]. 

Basically, the browser will rubber stamp requests if the source and the target share the same origin. But if you're interacting with an API, that's likely not the case.

That doesn't mean that cross-domain requests are off limits. But to carry them out, you need to satisfy a few conditions.

Check out [this page][cors_tutorial] for a much more thorough exploration of CORS and its implications for both the client and the server, but for now the most important thing to know is this: in order for a cross-origin request to succeed, the server needs to include an 'Access-Control-Allow-Origin' header, and that header needs to authorize the requesting URL (either specifically or via a `'*'` value).

**Cool, but I don't control the API's response headers...**

Right. You're probably not in a position to assure that your API supports the required response headers.

At this point, you have a few options. 

The first is to get in touch with people who host the API and request that they support cross-origin requests. It may be ignored or take awhile, but it's a path of least resistance that they have little reason to turn down beyond not having the time (an admittedly fair obstacle).

The second is to roll your own proxy server. Even if you can't get the API host to support cross-origin requests, you can make your own server that does - and then use it to hit the target API outside of the browser.[^1]

The third is to take advantage of other services that do the work for you. [This site][json_proxy], for example, will act as a proxy server for you, without any coding on your behalf. Simply append the target url to `https://jsonp.afeld.me/?url=`, and the CORS request will succeed.[^2]

**That's pretty easy...**

Indeed, it's not very difficult to get around the barriers to cross-origin requests. The main question is simply how much work you're willing/need to do given your use case.

[^1]: A [Rails API][rails_api] could do the trick.
[^2]: One thing to note about this solution is that it creates some security concerns. Since you'll be passing your data through a proxy you don't control, you'll probably want to exercise discretion concerning what sort of information you pass through. The proxy could intercept (a) sensitive data from the API, or (b) private API keys in the URL (which could be a [major problem][api_exposure]).

[ajax]: https://en.wikipedia.org/wiki/Ajax_(programming)
[cors]: https://en.wikipedia.org/wiki/Cross-origin_resource_sharing
[sop]: https://en.wikipedia.org/wiki/Same-origin_policy
[cors_tutorial]: http://www.html5rocks.com/en/tutorials/cors/
[json_proxy]: https://jsonp.afeld.me/
[rails_api]: http://edgeguides.rubyonrails.org/api_app.html
[api_exposure]: https://rosspenman.com/api-key-exposure/