---
layout: post
title:  "Backbone + React: Render Components With `Render`"
date:   2015-09-14
categories: javascript
tags: react backbone
--- 

>tl;dr - Use `React.render()` in place of `React.renderComponent()` when rendering React components from Backbone views

If you're looking to start bringing React into a project you've set up in Backbone, you're in luck. It can be done. There are even some nice posts out there to help get you started.

Say you want to start bringing React components into your Backbone views. Thomas Boyt [has you covered][venmo]. Looking to get more parts of each library talking to each other? Check out [Ben Lewis' post][engineyard].

The wealth of information is a gift, and a testament to the software community's willingness to share information to help make solving common problems a little bit easier.

Unfortunately, it can be tough for the literature to keep up with changing APIs. Techniques that got the job done only a year ago (or less) might no longer be supported today.

This isn't a huge problem; it's better to have slightly dated information than to have nothing at all. 

But if you're new to both libraries, trying to make React and Backbone play nicely with each other can be challenging enough. Adding on the obstacle of non-working sample code can make the solution seem out of reach.

So, in this post, I want to highlight one place where the API has changed, and what you can do to keep making progress.

**Goodbye `renderComponent()`**

Both of the posts linked to above render React components from Backbone views with `React.renderComponent()`. This function takes two arguments: the component to render and the DOM element that it should occupy.

The trouble is, that function [is now deprecated][rendercomponent]. If you give it a try with a recent version of React, you'll be met with an unwelcome surprise in the console: `Uncaught TypeError: React.renderComponent is not a function`.

If you read through the documentation, you'll see that `React.renderComponent()` has been replaced by `React.render()`. And that's the replacement you should use (see the "tl;dr"), but there are reasons you might be tempted to not do so.

Just like `React.renderComponent()`, `React.render()` requires that you pass in a DOM element identifying where on the page your component should be rendered. When the component is rendered to that DOM element, it replaces the existing contents of that container.

This means that rendering your React component can delete existing page contents. When you're displaying something like a modal, that can be a real pain.

**So Many Renders**

Taking a look through the documentation, you can see that `React.render()` is supplemented by `React.renderToString()` and `React.renderToStaticMarkup()`. These functions have the advantage of only taking one argument: the component you want to render.

The benefit of not taking control of a DOM element is that you don't have to cede control of that container over to React. Simply generate the contents you want to display, then add or remove them at will with jQuery; it creates no problems for your existing content.

However, such static markup does not have access to functions that you've defined on your components. When it comes time to handle events, you can lose a lot of the benefits that React provides, as you start micromanaging every event from your top-level Backbone view.

**Render Under Control**

In order to access functions defined on your React components, you'll want to stick with `React.render()`, handing over control of the DOM element. That's the most simple way to get your content on the page while also taking advantage of React's event handling.

If you're worried about losing existing content, a simple solution is to append a new DOM element where you want the component to render, passing that in as the container.

{% highlight coffeescript %}
render: ->
  $(@parentEl).append("<div class='component-container'></div>")
  React.render(
    React.createElement(MyComponent, { prop1: {}, prop2: [] }),
    $('.component-container').get(0))
{% endhighlight %}

Make sure that you unmount the component when it's closed:

{% highlight coffeescript %}
React.unmountComponentAtNode($('.component-container').get(0))
{% endhighlight %}

And you've successfully taken advantage of React's event handling inside of your Backbone view.

[venmo]: https://venmo-blog.squarespace.com/2015/6/4/using-react-components-as-backbone-views
[engineyard]: https://blog.engineyard.com/2015/integrating-react-with-backbone
[rendercomponent]: https://facebook.github.io/react/blog/2014/10/28/react-v0.12.html