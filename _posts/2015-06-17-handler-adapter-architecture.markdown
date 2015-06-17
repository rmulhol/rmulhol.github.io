---
layout: post
title:  "Handlers and Adapters: Thinking Server Architecture"
date:   2015-06-17
categories: Java
tags: server socket
--- 

In a [previous post][prev_post], I walked through how to build a simple http server in Java that echoes back whatever request is issued by the user. While it's a nice start for those new to server construction, it leaves a lot left to be desired in terms of functionality.

Of course, you could just continue to build on that `while` loop, constructing a gargantuan nested conditional to deal with different requests appropriately. But that will leave you with some really gross code.

Even following the tried and true approach of building away before extracting/refactoring code can leave you with a bit of a mess. It's hard to put together a good architecture for a complicated piece of software like an http server without an early familiarity with what it might look like down the road.

At least, that was the trouble I found myself in after an initial attempt. I managed to decouple and extract most buckets of behavior from the core `while` loop, but there was one place where things got iffy: the method where the server determined what response to issue based on the request.

This is not a good place to fail - it's basically the heart of the application. I knew the best approach would probably be to go ahead and rewrite the whole thing, but I was at a loss for how I could approach such a rewrite without duplicating the same error.

Luckily, after speaking with a few knowledgeable colleagues, I stumbled upon a role model of sorts: Clojure's [Ring][ring]. Ring is a library for building web applications with Clojure, and it's architecture provides some guidance as to how you can approach building an http server for yourself.

While I'd recommend that anyone interested visit [the Ring wiki][ring_wiki] for a more detailed explanation, the core of Ring's architecture is a division of responsibilities among Handlers and Adapters.

Handlers manage the matching of requests to responses, taking one map that defines a request and returning another that defines the response. Adapters do the rest: setting up a port, formatting input into a map for the handler, formatting a response map into output for the client, and so forth.

Dividing up responsibilities along this fault line has several advantages:

First, putting the request-response matching work in the handler makes the codebase more manageable and extensible. Changes to how input and output are parsed are decoupled from changes to how you respond to a particular request. You can test and verify that specific requests match their designated responses in the handler, and then deal with other application behavior elsewhere.

Second, the two major components of a handler/adapter architecture can be further subdivided as necessary. If you only need to deal with a small subset of responses, perhaps you can get away with a single, isolated handler. If responsibilities proliferate, the handler can be further subdivided into various support objects/namespaces for the main handler.

With a server architecture built around the separation of handlers and adapters, it's much easier to build a server you can be proud of. Good, decoupled code only gets you so far without a good, decoupled architecture, and the handler/adapter architecture lays the foundation for building up a good, decoupled code base.

[prev_post]: {% post_url 2015-06-06-simple-java-server %}
[ring]: https://github.com/ring-clojure/ring
[ring_wiki]: https://github.com/ring-clojure/ring/wiki