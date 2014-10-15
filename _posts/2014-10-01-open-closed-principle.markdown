---
layout: post
title:  "You Down With OCP? Implementing the Open/Closed Principle"
date:   2014-10-01 
categories: OOP
tags: SOLID ocp
--- 

Today, I had the privilege of sitting in on a lecture delivered by a more senior colleague that discussed the SOLID principles of object-oriented programming and design (**S**ingle responsibility, **O**pen/closed, **L**iskov substitution, **I**nterface segregation, and **D**ependency inversion). Though the talk touched on several features of SOLID , one of the tidbits I found most helpful was the discussion of the open/closed principle. 

Entering the talk, I knew what this principle denotes: software modules should be *open* for extension but *closed* for modification. I'd even credit myself with having moved from initially bewildered (I'm supposed to be able to add stuff without changing stuff? Isn't addition a form of change??) to tentatively self-assured (OK, I should be able to add *new* stuff without having to change *old* stuff) in my dealings with it. Still, being able to listen to someone who was intent on clarifying the concept was supremely helpful.

For me, the most illuminating takeaway was that implementing the OCP has a lot to do with dependency inversion. One of the ways that you make sure an object is closed for modification is by assuring that it can interact with new objects without having to know a bunch of details about the particularities of those additions. Instead of making an object that responds to other objects based on their identity, have it expect those other objects to be able to deal with it.

An example from the talk --- say I'm creating a game that includes both human and computer players. One way I could deal with that is like this:

{% highlight ruby %}
def next_move(player)
  if player.is_a? Human
    # do this
  elsif player.is_a? Computer
    # do that
  end
end
{% endhighlight %}

That seems like it'll work fine. But what if I want to add a new type of player (say I want to have several classes of computer players that operate at varying degrees of difficulty, for example)? To make that addition, I'm going to need to add a new `elsif` statement for the new player(s). And while that change might not seem like that big of a deal right here, imagine if I've structured multiple methods - or worse, multiple classes - with this pattern. One addition is going to force changes all over the place.

If I invert the dependency, and expect all of the different varieties of players to respond to a single method (using what Sandi Metz refers to as 'duck typing'), then I can turn abstraction to my advantage:

{% highlight ruby %}
def next_move(player)
  player.deliver_next_move
end
{% endhighlight %}

Now I trust each player to implement a `deliver_next_move` method, and my original object will work with any added class that does so. I'll be able to use the new additions without modifying the code I already have. Hence, open for extension, closed for modification.

I'm excited about continuing to play with and learn about these concepts (what's the difference between the O and the D in SOLID? Is one [just a way of implementing the other][ocp_v_dip]?), because it's clear that what bits I do learn will help me write better and more maintainable code.

If you're interested in further exploring the topic, I'd recommend checking out [this][uncle_bob] and [this][metz].  

[ocp_v_dip]: http://tech.michaelgroner.com/2009/01/29/openclose-principle-and-dependency-inversion-principle-ndash-two-sides-same-coin/
[uncle_bob]: http://blog.8thlight.com/uncle-bob/2014/05/12/TheOpenClosedPrinciple.html
[metz]: http://www.sandimetz.com/blog/2014/05/28/betting-on-wrong