---
layout: post
title: Always Think RESTful
author: David Padilla
email: david@crowdint.com
avatar: d32b52ec6403614b1adf3e648cbbe584
---

What I am about to describe in this post is probably something obvious to the
advanced Rails developer. Yet, it's sometimes hard to get the concept
when you come from other frameworks or technology.

I, at least, personally had a problem getting it when I first started
using Rails.

## REST

REST stands for *Representational State Transfer*.
What does that mean anyway? well, a lot of things, but, for now, I'll
just focus on what it means when designing a Rails app.

### The wrong way

I've seen it a lot of times. It's just easy to think that, let's say, a
OrderController, must always be tied to a Order model, and that,
all actions performed on the Order objects should be done via that controller.

So, with that thought on your mind, you end up with a controller like this:

{% highlight ruby %}

class OrderController < ApplicationController
  def new
    ...
  end

  def create
    ...
  end

  def index
    ...
  end

  def update
    ...
  end

  def review
    ...
  end

  def remove_review
    ...
  end

  def cancel
    ...
  end

  def pay
    ...
  end

  def return

  end
end

{% endhighlight %}

### What's wrong here?

If you take a look at it, there's all these actions on the controller
that shouldn't be there: review, remove_review, cancel, pay, return.

Thinking restful is thinking CRUD. Create, Retrieve, Update, Delete. It
means that you have *resources* and that with every request you are
creating, updating, deleting or retrieving for display that resource or
many of them.

A resource is not necesarily a model, it's just *something* that has a
state or that can be altered.

### How to make it right?

So, if we go back to our example, the right thing to do, is to think
about those *bad* actions as a resource.

If you're going to review an order, then, you need a *OrderReview*
controller.


{% highlight ruby %}

class OrderReviewController < ApplicationController
  def create
    ...
  end

  def delete
    ...
  end
end

{% endhighlight %}

See? Now we're thinking of the review as a *resource*, a resource that
can receive all the *CRUD* actions. In this specific case we *SHOULD* have
a *OrderReview* model and whatnot, but I just wanted to make an example
out of it.

A most common mistake... an action that actually alters the state of an
object like the cancel action.

I know you're, just changing some field from '*active*' to '*enabled*' or
something, but still, this looks much better:

{% highlight ruby %}

class OrderCancellationsController < ApplicationController
  def create
    ...
  end

  def delete
    ...
  end
end

{% endhighlight %}

In case you're wondering, the *delete* action would be used to cancel the
cancellation (it can happen).

You could now refactor all the other actions to their respective
controller: *PaymentsController*, *ReturnsController*.

## Associations

In terms of REST, *everything is a resource*. And the best way to think
how models relate to each other is by thiking that their relationship is
a resource.

For example, if you had a User that can add other User objects as its
friends, instead of creating a *add_friend* or something action, you'd add
a *Friendship* controller, with *create* and *delete* actions.

By the way, I've seen relationships like:

{% highlight ruby %}
class User < ActiveRecord::Base

  has_and_belongs_to_many :users ... # Plus a lot of weird code to make this work

end
{% endhighlight %}

*Always remember to think restful*, probably the user should:

{% highlight ruby %}
has_many :friendships
has_many :friends, :class_name => "User", :through => :friendships

# This actually requires more code to make it work, but, you get the
# point, right?
{% endhighlight %}

Think about it. Way better.

### But, why? You have to write all this "unnecesary" code

*Mantainability* and *Readability* mostly. If you're not thinking about
these two while writing your code, you're being rude to your collegues
or yourself.

Also, Rails its all about love and following conventions. This is one of
them.

Hope this guide helps you in some way, cheers!
