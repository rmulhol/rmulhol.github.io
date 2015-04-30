---
layout: post
title:  "The Template Method: Revisiting Inheritance in Rails Controllers"
date:   2015-04-30
categories: Ruby
tags: rails design
--- 

In a [previous post][prev_post], I discussed the importance of adhering to SOLID design principles when grappling with inheritance in Rails. Specifically, I talked about how the flow of inheritance from the `ApplicationController` to subordinate controllers can generate dangers of violating the Open/Closed and Liskov substitution principles.

We dealt with the Liskov substitution principle by refactoring this: 

{% highlight ruby %}
# ApplicationController
class ApplicationController < ActionController::Base
  include SessionsHelper # implements current_user?(user)
  before_action :redirect_if_incorrect_user

  ...

  def redirect_if_incorrect_user
    user = params[:user_id] ? User.find(params[:user_id]) : User.find(params[:id])
    unless current_user?(user)
      redirect_to root_url
    end
  end
end

# UsersController
class UsersController < ApplicationController
  # ApplicationController determines user from params[:id]
end

# EventsController
class EventsController < ApplicationController
  # ApplicationController determines user from params[:user_id]
end

# GuestsController
class GuestsController < ApplicationController
  
  ...
  
  private

    # Overwrites ApplicationController to find user by params[:event_id]
    def redirect_if_incorrect_user
      event = Event.find(params[:event_id])
      unless current_user(event.user)
        redirect_to root_url
      end
    end
end
{% endhighlight %}

Into this:

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

  # Determines user based on whichever params are present
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

The latter implementation conforms to Liskov because we're no longer overwriting a method from a superclass in a subtype in a way that would break the superclass. But it also introduces a violation of Open/Closed - `identify_user_in_params` would need to be modified each time we added a subtype. Accordingly, we further refactored to here: 

{% highlight ruby %}
class ApplicationController < ActionController::Base
  include SessionsHelper
  before_action :redirect_if_incorrect_user
  
  ...

  def redirect_if_incorrect_user
    # Delegates determination of user from params to subclasses
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

This is a pretty alright stopping point, since we're now delegating the work of identifying relevant parameters to the subtype. The `ApplicationController` is open for extension, and closed for modification - new subtypes need to implement `identify_user_in_params`, but don't require changes in the `ApplicationController`.

If you look closely at this last implementation, you may notice it looks a lot like an implementation of [the template method][template] design pattern. According to Russ Olsen, 

> In the Template Method pattern, the abstract base class controls the higher-level processing through the template method; the subclasses simply fill in the details.[^1]

That passage aptly describes what's going on here - the superclass implements shared behavior, and the behavior that varies is delegated to the subclasses. Only one thing is missing: the template!

Some might argue that there's no need for a template in Ruby. After all, if we implement a new subclass and doesn't either implement `identify_user_in_params` or include `skip_before_action :redirect_if_incorrect_user`, this code will throw an error: ``NameError: undefined local variable or method `identify_user_in_params` for #<...>``.

And, in fact, this very error introduces a deliberate violation of Liskov - no subtype is now substitutable for the superclass, since the `ApplicationController` by itself cannot function (we'll always get an error unless the subtype implements a workaround). Therefore, it's best to be explicit about what's going on. By introducing a template:

{% highlight ruby %}
class ApplicationController < ActionController::Base

  ...

  def identify_user_in_params
    raise "NotImplementedError: identify_user_in_params is not implemented for #{controller_name}"
  end
end
{% endhighlight %}

We make it obvious what we're expecting from our subtypes.

[^1]: Design Patterns in Ruby (Pearson Education: 2008), p. 65

[prev_post]: {% post_url 2015-04-27-solid-rails %}
[template]: http://en.wikipedia.org/wiki/Template_method_pattern