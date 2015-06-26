---
layout: post
title:  "What the Heck? Being Confused About Asynchronous Execution in JavaScript"
date:   2015-06-25
categories: JavaScript
tags: event variable
--- 

As I've been working on using my Java server as a web interface for my Clojure tic tac toe game, I've found JavaScript very helpful. I use it for form validation on the game configuration page:

{% highlight javascript %}
function validateGameConfig() {
    var player1Move = document.getElementById('player-1-move').value;
    var player2Move = document.getElementById('player-2-move').value;

    if(player1Move == " " || player1Move == "") {
        alert("Must enter a move signature for player 1.");
        return false;
    } else if(player2Move == " " || player2Move == "") {
        alert("Must enter a move signature for player 2.");
        return false;
    } else if(player1Move == player2Move) {
        alert("Player move signatures must be distinct.");
        return false;
    } else {
        return true;
    }
}
{% endhighlight %}

And for creating event listeners on each board cell that trigger when the user selects a move:

{% highlight html javascript %}
<h1>Tic Tac Toe!</h1>
<h4>Click An Open Space To Make A Move</h4>

<table>
  <tbody>
    <tr>
      <td class="square" id="square-1">
        <form id="form-1" method="post">
          <input type="hidden" name="player-1-id" value="human">
          <input type="hidden" name="player-2-id" value="ai">
          <input type="hidden" name="player-1-mv" value="X">
          <input type="hidden" name="player-2-mv" value="O">
          <input type="hidden" name="board" value=" , , , , , , , , ">
          <input type="hidden" name="move" value="1">
        </form>
      </td>

      ...

    </tr>
  </tbody>
</table>

<script type="text/javascript">
for (var i = 1; i < 10; i++) {
    (function () {
        var thisForm = 'form-' + i;

        document.getElementById('square-' + i).addEventListener("click", function() {
            document.getElementById(thisForm).submit();
        });
    }())
}
</script>
{% endhighlight %}

JavaScript has served it's purpose and opened my eyes to all that you can accomplish on the client side. But it's also left me scratching my head a few times.

The first example wasn't much trouble - it's relatively straightforward. 

But the second was a different story. I had to manually declare the variable `thisForm`; I couldn't just inline `'form-' + i` on the `getElementById` call.

Try it for yourself. If you replace the above code with this:

{% highlight html javascript %}
<script type="text/javascript">
for (var i = 1; i < 10; i++) {
    (function () {
        document.getElementById('square-' + i).addEventListener("click", function() {
            document.getElementById('form-' + i).submit();
        });
    }())
}
</script>
{% endhighlight %}

Everything breaks. Or, more accurately, fails silently - the form is not submitted when a cell is clicked. 

So, what's going on here?

The problem relates to the nature of asynchronous listeners in JavaScript. Inside of the `for` loop, the var `i` is set to some integer between 1 and 9. As that's happening, the elements defined by the id `'square-' + i` are receiving event listeners that trigger when clicked.

But the listener is not triggered at that moment - otherwise all the forms would be submitted when the script ran. Instead, the function to submit the form lies in wait for the event listener to trigger.

But there's a problem. When the event triggers, the var `i` is no longer set to the value it was when the `for` loop was executing - now it's at 10 (mutable data, good stuff).[^1] So when you click on a cell, you get the following error in the console:

{% highlight javascript %}
Uncaught TypeError: Cannot read property 'submit' of null
{% endhighlight %}

By the time the asynchronous listener is called, the object returned by `getElementById` is null, since `'form-' + i` evaluates to `'form-' + 10` (or just `'form-10'`), which doesn't correspond to any ID on the page. 

Asynchronous callbacks can be tremendously confusing - they can lead you into the pit of [callback hell][callback_hell]. 

But they're also tremendously useful. They let you delay behavior until it's needed, execute multiple actions at once, and avoid blocking.

The trick is just figuring out how and when to use them, and then developing thick skin for whenever they leave you feeling like pulling your hair out.

[^1]: Special thanks to Colin and Geoff for helping me to figure this stuff out. Another wildly helpful resource from Geoff: [wat][wat].

[callback_hell]: http://callbackhell.com/
[wat]: https://www.destroyallsoftware.com/talks/wat