---
layout: post
title: Auditrail, how to play dirty with callbacks. 
author: Luis Galaviz
email: luis.galaviz@crowdint.com
avatar: 1e7f8fb8733b6193cf3bdbc85693f515
---

Recently, we've been working in a very interesting project here at Crowd Interactive. We needed to audit some of our models
in order to keep track of their changes, among other things. We searched for options (you know, plugins, gems, etc.)
to help us accomplish that task. We found a few ones, but eventually we came to a dead end when those tools
were not fitting our expectations, they were either too complicated or too rough for our needs. So, we decided to
create a new solution named [auditrail](http://github.com/crowdint/auditrail).

Auditrail is a gem that provides a highly configurable solution to audit your models. It provides two generators.
The first one creates the Audit model with a couple of methods that helps you use it, and the second creates the migration for its table.

## How is it used?

Quite simple. You just need to install the *auditrail* gem, and then, run the generators provided
(*auditrail:migrations* and *auditrail:model*). Then you just need to call the method *auditable*
in the models you want to audit, and, that's it!

## How does it work?

Auditrail was built to make it easily and readable. So, if you take a quick look at the source code
you can identify all the components.

* Auditrail keeps track of the changes in the audited models through the Audit model. Here, you can configure your own methods to manipulate those changes, or, you can place your filters or special actions.

* Auditrail is a module that extends *ActiveRecord::Base*, so you can use it just by calling the *auditable*
method on the models you want to Audit. Simple, isn't it?

* In order to manipulate the plugin requests auditrail uses a basic DSL to handle the method calls.
Those methods where built to specify a list of the attributes that you want to audit
and/or include the actor that is making the changes to the audited model.

* It extends the ActiveSupport::Concern to facilitate the manipulation of the instance methods and the
class methods in the same module, dealing with the necessity to include an extend for the ActiveRecord::Base. Example:

{% highlight ruby %}
module Auditrail
  extend ActiveSupport::Concern

  module ClassMethods

    def some_method
      # Include some code for your class method...
    end
  end

  module InstanceMethods

    def track_changes(*options)
      # Include some code for your instance method...
    end
  end
end

ActiveRecord::Base.send(:include, Auditrail)
{% endhighlight %}

* Auditrail uses ActiveRecord *callbacks* to trigger the methods that actually audit the models.

* Once auditrail knows when an object is modified it will track the changes using *ActiveModel::Dirty*.

* ActiveModel::Dirty returns a hash that we serialize in order to save them in our Audit model.

* All these changes will be stored in the *dumped_changes* field on the audits table.

*The dumped_changes fields is generated with type **string** by default, in case you're planing to track big models,
you should change its type to **text**.

Here you can find a list with the attributes on the Audit model:

{% highlight ruby %}
==============  ====      ===========================================================================
Attribute         Type      Usage
--------------  ----      ---------------------------------------------------------------------------
:dumped_changes    text      Keep the changes of the model serialized.
:action            string    Indicates whether was creation ("creating") or an update ("updating").
:model_changed     string    Indicates the model that changed.
:element_id        integer   Indicates the element from the audited model that has changed.
:invoker           string    Optional, you can chose if you want to store the action invoker (user).
:timestamps                  You can use the timestamps to determine when the audited model has changed.
==============  =======   ===========================================================================
{% endhighlight %}

As with all open source projects, feel free to collaborate with this
gem by forking it on github.
