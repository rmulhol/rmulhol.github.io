---
layout: post
title:  "Working With APIs: Rolling a Proxy Server"
date:   2015-08-12
categories: javascript
tags: node cors
--- 

In [yesterday's post][prev_post], we talked about the difficulties one can encounter when making AJAX requests to an API that doesn't support cross-origin resource sharing (CORS). Unless the response includes the required `Access-Control-Allow-Origin` header, you'll be getting an error message in the console - and not much else.

One way to get around this is to write your own proxy server. Instead of waiting for your API's maintainers to implement support for CORS, simply fetch the content with your own proxy and pass it along with the required header tacked on. 

If you were going to do this in production, you'd have a number of concerns outside of that core functionality - hosting, authentication, etc. But in development, you can cut away a lot of that overhead. Implementing a proxy server in Node is quick and easy.

Say we're putting together a little app to display and generate memes. One good option for doing so is [Imgflip][imgflip]. They support fetching popular memes and adding captions on demand. Good stuff.

They also don't support CORS. Try to hit the API with AJAX, and you'll see the familiar error message.

> XMLHttpRequest cannot load https://api.imgflip.com/get_memes. No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'http://localhost:8080' is therefore not allowed access. 

If you wanted to hit it by proxy, say at `localhost:3000/get_memes`, you could do so with this script:

{% highlight javascript %}
var http = require('http');

http.createServer(proxyImgflip).listen(3000);

function proxyImgflip(request, response) {
  console.log("Serving up " + request.method + " " + request.url);

  response.setHeader('Access-Control-Allow-Origin', '*');

  var proxyRequest = {
    hostname: 'api.imgflip.com',
    port: 80,
    path: request.url,
    method: request.method
  };

  var proxyResponse = http.request(proxyRequest, function(res) {
    res.pipe(response);
  });

  request.pipe(proxyResponse);
}

console.log("Listening on 3000");
{% endhighlight %}

Assuming you've got Node installed, you can run this script with `node <filename.js>`, and you're good to go. To access `https://api.imgflip.com/get_memes` in a cross-domain request, you could just hit `http://localhost:3000/get_memes`.

How does it work? 

We use Node's `http` API to create a server, passing in a callback and setting it to listen on port 3000. 

Within the callback, we log the request, affix the required header to our response, and pass along the incoming request to Imgflip's API, piping the data we get back into our response.

It's only a development-caliber solution, but it delivers major payoff given how little effort is involved. If you're looking to develop a client-side application to work with an API that doesn't support CORS, you can get most of the way there with a simple Node script.

[prev_post]: {% post_url 2015-08-11-cross-origin-resource-sharing %}
[imgflip]: https://api.imgflip.com/