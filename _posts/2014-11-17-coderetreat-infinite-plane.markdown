---
layout: post
title:  "To Infinity and Beyond: Working through Conway's Game of Life at Global Day of Coderetreat 2014"
date:   2014-11-17
categories: general
tags: coderetreat
--- 

This past Saturday, I had the opportunity to participate in the [Global Day of Coderetreat][gdc]. It was the first time I had ever participated in a code retreat, and I had a lot of fun working with new people, operating under a diverse set of constraints, and iteratively re-approaching the same problem from different angles.

For this event, our challenge was to code [Conway's Game of Life][cgl]. I had never worked on this game before, and I found it to be a perfect fit for the event: a larger problem might have been unmanageable and frustrating, but a smaller one could have been too simple and uninteresting.

What made things really fun was the changing set of constraints. We would spend 45 minutes working with one person to write a version within a particular set of limitations (don't use conditionals, don't write methods with more than 3 lines of code, try out [TDD as if you meant it][tdd_asif], etc.), then toss out the code, change pairs, and take another shot with a different set of constraints.

Deleting the code after each session was oddly liberating. I would have expected it to be really frustrating ("I already tackled this task, I want to move onto the next one!"), but in reality, hitting the reset button and starting from the ground up with the accumulated knowledge of what worked and what didn't yielded better code on each iteration.

I also appreciated getting a sense for how to work within an infinite two-dimensional field. In the past, when I've worked with a grid structured similarly to the one in this game, I've made calculations for determining neighboring cells based on the length of the rows and columns. 

{% highlight ruby %}
# grid of finite width and height:

 0 | 1 | 2
---|---|---
 3 | 4 | 5
---|---|---
 6 | 7 | 8
{% endhighlight %}

This setup is sufficient for me to determine lines. I know that the rows will be sequential sets that begin from the base index and extend as long as the side length ([0, 1, 2], [3, 4, 5], [6, 7, 8]), columns will be cumulative lists that begin at each of the indexes in the first row and add the length of the side ([0, 3, 6], [1, 4, 7], [2, 5, 8]), and diagonals will be cumulative lists that begin at either the first or last index in the first row and then add the length of the side plus 1 or the length of the side minus 1, respectively ([0, 4, 8], [2, 4, 6]).

With the Conway's Game of Life, however, using such calculations to assess the neighbors would be to impose finitude on an infinite field. How can we calculate the neighbors for a cell if each row and column stretches out infinitely?

The answer, I learned, is to work with a [Cartesian coordinate system][plane]. If we replace some base cell (say, index 4 in the example above) with (0, 0) and work outward from there, we can learn the index of all neighbors simply by toggling the x and y values by one. You can do that pretty easily:

{% highlight ruby %}
def get_neighboring_cells row, col
  increments = [-1,0,1].repeated_permutation(2).to_a - [[0,0]]
  increments.map do |neighbor_row, neighbor_col|
    neighbor_row += row
    neighbor_col += col
    [neighbor_row, neighbor_col]
  end
end
{% endhighlight %}

Here, we get all repeated permutations of [-1, 0, 1] of length 2 ([[-1, -1], [-1, 0], [-1, 1], [0, -1], [0, 0], [0, 1], [1, -1], [1, 0], [1, 1]]), remove [0, 0] (which maps to the index of the cell for which we're gathering neighbors), and then map those values onto the cell we've passed in. The result is an array containing the coordinates for all 8 neighbors:

{% highlight ruby %}
> get_neighboring_cells(5, 5)
=> [[4, 4], [4, 5], [4, 6], [5, 4], [5, 6], [6, 4], [6, 5], [6, 6]]
{% endhighlight %}

Working with coordinates like this isn't all that tricky, but it's something you might not think of if you're continually approaching similar problems in similar ways. As Thanksgiving nears, I'm thankful that I had the opportunity to practice tackling a different problem in different ways at Global Day of Coderetreat 2014.

[gdc]: http://globalday.coderetreat.org/
[cgl]: http://en.wikipedia.org/wiki/Conway's_Game_of_Life
[tdd_asif]: http://coderetreat.org/facilitating/activities/tdd-as-if-you-meant-it
[plane]: http://en.wikipedia.org/wiki/Cartesian_coordinate_system