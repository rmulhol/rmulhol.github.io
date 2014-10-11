---
layout: post
title:  "Programmer Beware: Potential Problems Getting User Input in Ruby"
date:   2014-10-11 
categories: io
--- 

Does your program rely on user input? Does it require users to enter an integer? Do you use user input for other calculations in your program? If you've answered yes to any of these questions - or, god forbid, all of them - then there are some things you should be aware of. Your program may not run as you expect. It may crash at times you don't expect.

**Thing You Should Know #1: The Absence of Input is Input**

Maybe you want to write a little program that prompts the user for an integer. To be sure you received what they intened, you want to offer the user a chance to confirm their input. To make sure you can deal with numerous variations of "yes", you convert the input to lower case and match the first letter against "y":

{% highlight ruby %}
def get_and_confirm_integer
  user_integer = get_integer
  user_confirmation = confirm_integer(user_integer)
  until input_confirmed?(user_confirmation)
    user_integer = get_integer
    user_confirmation = confirm_integer(user_integer)
  end
  puts "Great, thanks for entering #{user_integer}!"
end

def get_integer
  print "Enter an integer: "
  user_integer = gets.chomp
  user_integer
end

def confirm_integer(user_integer)
  puts "Just confirming, you did mean to enter #{user_integer}?"
  user_confirmation = gets.chomp
  user_confirmation
end

def input_confirmed?(input)
  input[0].downcase == "y"
end
{% endhighlight %}

This all works well and good until a user decides to stonewall your program. Your `input_confirmed?` method calls `.downcase` on the first element of `user_confirmation`, but the absence of a response will equate to `nil`. Your attempt to call `.downcase` on `nil` will provoke an unfortunate message: `undefined method 'downcase' for nil:NilClass (NoMethodError)`.

Of course, the easiest way to hedge against this error would simply be to avoid calling `.downcase` on a value that might return `nil`:

{% highlight ruby %}
def input_confirmed?(input)
  input[0] == "y" || input[0] == "Y"
end
{% endhighlight %}

This program will continue running until the user enters a string that starts with a 'y' (which, hopefully, confirms that they intended to enter the integer you received), without throwing the `NoMethodError` you get by calling `.downcase` on `nil`. 

**Thing You Should Know #2: Strings Evaluate to Zero**

Say you want to add another layer of confirmation to your program. You want to validate not only that the user has entered what *they* intended to enter, but also that the input conforms to what *you* are expecting (in this case, an integer).

You know you can't just check whether the value you get returned from `gets` is an integer, since that method always returns a string. So you might opt to call `.to_i` on the string, and then check whether that call returns a valid integer.

This, however, will get you into trouble. The method `.to_i`, unfortunately (fortunately?), is set to move along happily converting strings that are not integers into 0. This saves you from a pesky exception near the `.to_i` call, but it can result in some faulty calculations when you use that 0 elsewhere.

{% highlight ruby %}
> "not an integer".to_i
=> 0
{% endhighlight %}

Possible solution: check that a 0 returned by `.to_i` is actually the result of the string "0":

{% highlight ruby %}
def user_input_is_valid?(user_input)
  user_input.to_i == 0 ? user_input.strip == "0" : true
end
{% endhighlight %}

This method will validate that a 0 you get from `.to_i` was a 0 to begin with, and allow other integers to proceed unheeded.

**Takeaway: Trust No One**

Though the above examples offer ways to avoid potential errors, the larger takeaway offers more general application. It is: don't trust your users to do what you expect. It's good to set up confirmations that guide the user onto the paths you've chosen, and to double check that your guidance is being heeded. If you don't, you'll be sure to find numerous other instances where users are doing things you don't expect. And, more than likely, that'll be causing your program to operate in ways you didn't expect.