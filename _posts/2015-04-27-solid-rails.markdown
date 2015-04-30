---
layout: post
title:  "Inheritance in Rails Controllers: SOLID Principles In Practice"
date:   2015-04-27
categories: Ruby
tags: rails design
--- 

One habit I've picked up in the interest of making sure my programs adhere to good design principles is to generally prefer [composition over inheritance][comp_over_inh]. When I'm seeking to include behavior in a class, I'll attempt to implement it directly or acquire it via [dependency injection][depend_inj] before I opt to inherit it from another class.

But good design is always relative to its context/environment, and sometimes inheritance makes sense. For example, Rails makes use of inheritance to share behavior while keeping code DRY. Following [convention over configuration][conv_over_conf], you'll get used to class declarations that look like this: 

{% highlight ruby %}
class UsersController < ApplicationController
{% endhighlight %}

Good design in Rails doesn't have to come into conflict with good design more generally. But it can be somewhat of a minefield. In this post, I want to walk through a recent design conundrum I encountered in order to situate how good design in Rails can be brought into greater harmony with [SOLID design principles][solid].

**The Danger Zone: The Application Controller**

Recently, I've been putting together [a little Rails app to handle RSVPs][my_app]. One piece of behavior I've wanted to implement is a method to verify that the user attempting to invoke an action actually has access to that action. It would be a problem if one user could modify another user's profile, so I need to have a filter. On the `UsersController`, it might look something like this:

{% highlight ruby %}
class UsersController < ApplicationController
  include SessionsHelper # contains current_user?(user)
  before_action :redirect_if_incorrect_user, only: [:edit, :update, :destroy]

  ...

  private
    def redirect_if_incorrect_user
      unless current_user?(User.find(params[:id]))
        redirect_to root_url
      end
    end
end
{% endhighlight %}

But I want to protect actions on other controllers as well. Specifically, a user `has_many :events`, and I don't want one user to be able to modify another user's events. So I can move the before filter to the application controller.

{% highlight ruby %}
class ApplicationController < ActionController::Base
  include SessionsHelper
  before_action :redirect_if_incorrect_user

  ...

  def redirect_if_incorrect_user
    user = params[:user_id] ? User.find(params[:user_id]) : User.find(params[:id])
    unless current_user?(user)
      redirect_to root_url
    end
  end
end
{% endhighlight %}

Now my before filter will be inherited by every controller, and I can un-protect individual actions as necessary by [whitelisting][whitelist]. I had to implement a slightly more tedious way of examining the route, since a route pointing to an action on the events controller will identify the user with `params[:user_id]` (rather than `params[:id]`, which will refer to the event itself), but this works for both users and events.

**OK, Let's Just Make Things A Bit Worse**

Astute readers will already notice an OCP violation, but let's dig a bit deeper into the weeds before we clean things up. 

In addition to events, a user `has_many :guests, through: :events`. Routes headed for the guests controller have `params[:event_id]` and `params[:id]`. That breaks the method above since it'll see that there's no `params[:user_id]` and try to find a `User` based on `params[:id]`, which actually identifies a guest.

Since the `before_action` filter is inherited, we can implement a better version on the guests controller by overwriting the `redirect_if_incorrect_user` method:

{% highlight ruby %}
class GuestsController < Application Controller
  
  ...
  
  private

    def redirect_if_incorrect_user
      event = Event.find(params[:event_id])
      unless current_user(event.user)
        redirect_to root_url
      end
    end
end
{% endhighlight %}

This will successfully override the method-of-the-same-name defined in the application controller and allow the filter to play nicely with actions on our guests controller, but we're playing pretty fast and loose with inheritance at this point. This is why we can't have nice things?

**SOLID Salvation: Or, the Open and Shut Case For Liskov**

We've got two pretty big violations of the SOLID design principles here:

1) [The Liskov Substitution Principle][liskov]: According to Robert Martin, this principle boils down to a rule that "subtypes must be substitutable for their base types" ([PPP][ppp] 111). In our case, the guests controller's implementation of `redirect_if_incorrect_user` violates the Liskov substitution principle because it can't be substituted for the implementation in the base class (the application controller). If we're locating the user with the `params[:event_id]`, we'll be out of luck when there is no `params[:event_id]` (e.g. actions on the users and events controllers).

2) [The Open-Closed Principle][ocp]: describing the OCP, Martin writes, "Software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification" ([PPP][ppp] 99). If we want to add new features or classes, we shouldn't have to go back and modify classes we already had to make the new ones work. That means it's not an adequate solution to add a new `if` statement to a method on the application controller each time we're dealing with a new controller. 

To accomodate Liskov, we could make the method on our application controller more universal:

{% highlight ruby %}
class ApplicationController < ActionController::Base
  include SessionsHelper
  before_action :redirect_if_incorrect_user
  
  ...

  def redirect_if_incorrect_user
    user = identify_user_in_params
    unless current_user?(user)
      redirect_to root_url
    end
  end

  def identify_user_in_params
    if !params[:user_id].blank?
      User.find(params[:user_id])
    elsif !params[:event_id].blank?
      Event.find(params[:event_id]).user
    else
      User.find(params[:id])
    end
  end
end
{% endhighlight %}

Here, the `identify_user_in_params` still doesn't satisfy Open/Closed, since it'll have to be modified as parameters change. Therefore, a more appropriate decision might be to delegate the work of correctly unpacking parameters to each controller:

{% highlight ruby %}
class ApplicationController < ActionController::Base
  include SessionsHelper
  before_action :redirect_if_incorrect_user
  
  ...

  def redirect_if_incorrect_user
    user = identify_user_in_params
    unless current_user?(user)
      redirect_to root_url
    end
  end
end

class UsersController < ApplicationController

  ...

  private

    def identify_user_in_params
      User.find(params[:id])
    end
end

class EventsController < ApplicationController

  ...

  private

    def identify_user_in_params
      User.find(params[:user_id])
    end
end

class GuestsController < ApplicationController

  ...

  private

    def identify_user_in_params
      Event.find(params[:event_id]).user
    end
end
{% endhighlight %}

[comp_over_inh]: http://en.wikipedia.org/wiki/Composition_over_inheritance
[depend_inj]: http://en.wikipedia.org/wiki/Dependency_injection
[conv_over_conf]: http://en.wikipedia.org/wiki/Convention_over_configuration
[solid]: http://en.wikipedia.org/wiki/SOLID_%28object-oriented_design%29
[my_app]: https://rocky-ocean-5667.herokuapp.com/
[whitelist]: http://en.wikipedia.org/wiki/Whitelist
[liskov]: http://en.wikipedia.org/wiki/Liskov_substitution_principle
[ppp]: http://www.amazon.com/Software-Development-Principles-Patterns-Practices/dp/0135974445
[ocp]: http://en.wikipedia.org/wiki/Open/closed_principle