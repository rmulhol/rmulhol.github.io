---
layout: post
title:  "Appreciating Open Data: Check Out Chicago Beaches"
date:   2015-08-20
categories: general
tags: react github
--- 

This past Tuesday, I attended my first [Chi Hack Night][chn]. It was a really cool experience. The event brings together folks from a broad variety of different backgrounds (not just developers) to work toward making a social impact.

Many things about the group are impressive: the kind of work they do and have done, the talent and motivation of the participants, and their welcoming attitude toward newcomers. The folks who organize and contribute to Chi Hack Night are absolutely awesome.

But, beyond the group itself, the tools they use to are impressive as well. In trying to help people visualize and grapple with what's going on in their community, the folks at Chi Hack Night are aided by access to [a tremendous amount of data][chi_data] provided by the city of Chicago.

I hope to go back to Chi Hack Night, and to contribute to some their projects. But, for now, I was also interested in exploring what all of this data looks like, and what I can do with it.

So, I started poking around, and I came across [a set of data][beach_data] that provides hourly readings from sensors along Lake Michigan that measure the water quality at those locations.

I decided to make [a little site][chi_beaches], where you can go to check out the latest readings - decide whether or not today would be a good day to hit up the beach in Chicago, or if it'd be best to stay out of the water.

The site isn't groundbreaking, but it's got a couple cool features:

1) Static - the Chicago Beaches page is a static site. All API requests are made through the browser (thanks, Chicago, for supporting cross-origin resource sharing). This probably makes it pretty uninteresting if you're browsing without JavaScript enabled and/or on an old browser, but it also means I get ...

2) Hosting - via GitHub pages. Just pushed up my `gh-pages` branch, and the site was live within minutes. It's amazing how easy it is to deploy a little React app with these kinds of tools. 

There's a few more things I could imagine doing to improve this site, but I'm even more excited at the possibilities that lie ahead working with the tools I've used. 

With a budding comfortability with React, huge amounts of data just waiting to be used, and simple static site hosting via GitHub pages, there's a lot of cool things that lie within reach.

[chn]: http://chihacknight.org/
[chi_data]: https://data.cityofchicago.org/
[beach_data]: https://data.cityofchicago.org/Parks-Recreation/Beach-Water-Quality-Automated-Sensors/qmqz-2xku
[chi_beaches]: http://rmulhol.github.io/chicago-beaches/