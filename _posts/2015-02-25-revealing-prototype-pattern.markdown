---
layout: post
title:  "Object-Oriented JavaScript: Implementing the Revealing Prototype Pattern"
date:   2015-02-25
categories: JavaScript
tags: design patterns
--- 

Coming to JavaScript from a language like Ruby or Java, object-oriented programming feels different. There are some [great resources][intro_oojs] out there to help you make the transition, but it can take some time (and experience) to get used to JavaScript's prototype-based, rather than class-based, object model. 

One of the things that I found particularly tricky was figuring out how to simultaneously create objects efficiently and incorporate information hiding. 

These two things are not too difficult to achieve on their own: 

-- If you want to create objects efficiently, you can place shared functions in a prototype rather than a constructor. That way, the functions aren't copied for every object; they're stored in the same prototype that each object (instance) references.

-- If you want to hide information on your objects, you can use the [revealing module pattern][rmp]. Creating an object returns a set of public properties that can access private variables and functions that are otherwise not accessible outside of the closure.

The problem is that these two options don't play nicely together. Private information stored inside the closure you used to implement the revealing module pattern isn't accessible to the prototype, and no information in the prototype is hidden. Is there a way to have your cake and eat it too?

Short answer: yes. Enter the [revealing prototype pattern][rpp].

Once you're familiar with the revealing module pattern, implementing the revealing prototype pattern isn't a giant leap. Both use something similar to an [immediately-invoked function expression][iife] to return objects with only those properties that you want exposed.  

For example, say we're building an AI player for a tic tac toe game. We know that we want the player to have a move signature and to be able to make a move. To make a move, it might need to implement a few other functions (a minimax algorithm, a function to score the current state of the board, etc.). 

If you just used the revealing module pattern, it might look like this:

{% highlight javascript %}
var AiPlayer = function() {
  var moveSign;

  function setMoveSign(moveSign) {
    ...
  }

  function getMoveSign() {
    ...
  }

  function makeMove(board) {
    ...
  }

  function minimax(board, myTurn, depth) {
    ...
  }

  function scoreBoard(board) {
    ...
  }

  return {
    setMoveSign: setMoveSign,
    getMoveSign: getMoveSign,
    makeMove: makeMove
  }
};
{% endhighlight %}

This option effectively hides information so that we're only exposing three properties (get/set the move signature and make a move), but it doesn't enable us to create new players efficiently. Any time we call `AiPlayer()`, all of the methods will be copied for the new instance.

Using prototypes, we could rectify this problem:

{% highlight javascript %}
function AiPlayer(moveSignature) {
  this.moveSignature = moveSignature;
}

AiPlayer.prototype = {
  getMove: function(board) {
    ...
  },
  minimax: function(board, myTurn, depth) {
    ...
  },
  scoreBoard: function(board) {
    ...
  }
}
{% endhighlight %}

Now we can create new objects efficiently. Calls to `new AiPlayer(moveSignature)` don't copy all of our functions, just a reference to the prototype where they're stored. The problem is that we've lost our information hiding - the `minimax` and `scoreBoard` functions are exposed publicly.   

Using the revealing module pattern, we can get information hiding *and* efficient object creation:

{% highlight javascript %}
function AiPlayer(moveSignature) {
  this.moveSignature = moveSignature;
}

AiPlayer.prototype = (function() {
  function getMove(board) {
    ...
  }

  function minimax(board, myTurn, depth) {
    ...
  }

  function scoreBoard(board) {
    ...
  }

  return {
    makeMove: makeMove
  }
})();
{% endhighlight %}

Using the revealing prototype pattern, our constructor creates objects efficiently and private information is hidden. New `AiPlayer` objects only store their own `moveSignature` and a reference to the prototype, and the prototype only exposes a `makeMove()` function.

[intro_oojs]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Introduction_to_Object-Oriented_JavaScript
[rmp]: http://addyosmani.com/resources/essentialjsdesignpatterns/book/#revealingmodulepatternjavascript
[rpp]: http://blog.pluralsight.com/revealing-prototype-pattern-structuring-javascript-code-part-iv
[iife]: http://benalman.com/news/2010/11/immediately-invoked-function-expression/