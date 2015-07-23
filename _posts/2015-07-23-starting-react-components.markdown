---
layout: post
title:  "Getting Started With React - Part 2: Exploring Components"
date:   2015-07-23
categories: javascript
tags: react components
--- 

React is a library for building user interfaces, but most of the work we've done so far has only laid the groundwork for actually carrying out that task. The UI we left off with doesn't exactly expose React's power.

![browser-view-hello-world]({{ site.url }}/assets/browser-view-hello-world.png)

If we want to start building something that looks better and does more, we should start to get a handle on React's tool for generating views: components.

Components are modules that include a `render()` method that tells the program what to display. They can generate content dynamically, responding to data passed in by other components or the user. 

Once you start taking advantage of their numerous features and piecing them together, components provide a valuable tool for constructing sophisticated user interfaces. 

Let's take a look at how components can help us transform our little "hello, world" app into something a bit more visually appealing...

**Before We Begin: Housekeeping**

If you're just hopping on now, you can access the source code for the work we've done so far in the second commit of the [demo-react-app Github repo][demo-react-app].

Before we begin coding up components, we'll want to install a few additional tools. The first is [react-bootstrap][react-bootstrap], which will provide a quick and easy way to add some styling. You can install it by running `npm install --save react-bootstrap`. You'll also want to add a link to the Bootstrap stylesheet in the `<head>` of your `index.html` file:[^1]

![index-with-bootstrap]({{ site.url }}/assets/index-with-bootstrap.png)

Next, we'll want to install [Watchify][watchify], which will enable us to automatically recompile our `js/bundle.js` file each time we make an edit to our source code. Add it by running `npm install --save-dev watchify`, and then add a `start` script to your `package.json` file:[^2]

![pkg-with-start-script]({{ site.url }}/assets/pkg-with-start-script.png)

Now we can run `npm run start` to initiate our `watchify` script, spin up an `http-server` in our project directory, and see changes reflected in the browser almost immediately as we save them.

**Step 1: Extract A Component**

Where last left off, we were including the full view for our app in the `js/app.js` file:

{% gist dfcb2fd783ada9654894 %}

This works fine when we've got so little display, but it quickly becomes unmanageable as our view becomes more substantial. The solution, in React, is to delegate the work to components.

Let's start by moving the JSX code we've got in the `js/app.js` file over to a new `js/components/home_page.js` file. Now our `js/app.js` file can look like this:

{% gist 8e7a24fddd37ab15ff70 %}

The `<HomePage />` call exhibits React's syntax for instantiating components in JSX - they exist as their own element on the page (with whatever components reside on them nested further down).

To mae this work the same as before, we need the `js/components/home_page.js` file to look like this:

{% gist 7cee0ed45ea4620b7310 %}

If we load the page in the browser, everything will look the same. But we've introduced a valuable tool: now we can apply the same technique to nest multiple components.

**Step 2: Nesting Components**

Say, for example, we want to add a header, a body, and a footer to our site. This is a great opportunity to start nesting components.

Instead of having the home page store all of the content for the site, it could instantiate several other components that would each handle one part of the view.

To do so, we's want to change our `js/components/home_page.js` file to contain the three subordinate components: `<Header />`, `<Body />`, and `</Footer>`.[^3]

{% gist cdef870ab3a9deca4967 %}

Once that's in place, we need to create the subordinate components.

Here's a `js/components/header.js` file that takes advantage of some react-bootstrap components to create a Navbar that's fixed to the top of the page:

{% gist 4d5e127a79a56f24ce07 %}

A `js/components/body.js` file to welcome our users:

{% gist 27f433cba607631a9346 %}

And a `/js/component/footer.js` file to round things out:

{% gist 4dbfee934b2fa85249fa %}

At this point, our page starts looking a lot better:

![browser-view-with-header-and-footer]({{ site.url }}/assets/browser-view-with-header-and-footer.png)

And we've got the added bonus that things are increasingly separated out from one another: we can independently edit the header, body, or footer for the page without too much concern about the others.

**Step 3: Dynamic Content**

The page is starting to look a little better, but it still isn't very "reactive" - we basically could have put everything together with some HTML and CSS. What more can we get out of components?

Say we wanted to add a button that would toggle the image users see on the body of the page. Instead of always seeing the React logo, they could click the button to see the JavaScript logo (and back and forth ad infinitum).

An easy way to implement this in React is to use the component's properties and state. Properties are values which should never change, while state is explicitly mutable via the `setState()` method.

Properties can be passed into a component when it's instantiated, but they can also be set with the `getDefaultProps()` method. Similarly, initial state can be set via `getInitialState()`.

If we wanted to add this new functionality entirely within the body component, we could do so like so:

{% gist c11e477b504fe2b552e0 %}

Here, we set the urls of the two image logos as properties of our Body component (since they will never change), then set the initial state with `showReactLogo` set to `true`. When the user clicks the button, we call `toggleImage()` to set `showReactLogo` to the opposite state of whatever it was. Within our `render()` call, we determine which image to show based on current state.

The result is that users can toggle the image back and forth by clicking our button. Our app is reacting to something!

![browser-view-with-image-toggle]({{ site.url }}/assets/browser-view-with-image-toggle.png)

**Wrap Up**

We've definitely made some improvements to our demo app by starting to work with components. We're now displaying formatted text, incorporating images, and changing things on the fly. We've also locked down a less intrusive build process for faster feedback loops.

But we've still barely scratched the surface of all we can do with React. Stay tuned for part 3!

[^1]: To access the latest version of the Bootstrap stylesheet, head over to [Bootstrap's Getting Started page][bootstrap-getting-started] and check out the Bootstrap CDN.
[^2]: I've added the most simple version of the `start` script here, but you may find it helpful to add the `-v` flag to get more verbose output each time your `js/bundle.js` file is recompiled.
[^3]: Note the closing tags - JSX requires closing tags even in places where you might normally be able to get away with leaving them off.

[demo-react-app]: https://github.com/rmulhol/demo-react-app
[react-bootstrap]: http://react-bootstrap.github.io/
[watchify]: https://github.com/substack/watchify
[bootstrap-getting-started]: http://getbootstrap.com/getting-started/