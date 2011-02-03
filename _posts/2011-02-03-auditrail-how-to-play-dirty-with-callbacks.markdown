---
layout: post
title: Auditrail, how to play dirty with callbacks. 
author: Luis Galaviz
email: luis.galaviz@crowdint.com
avatar: 1e7f8fb8733b6193cf3bdbc85693f515
---

We've been working recently in a very interesting project here at CrowdInt. Among other things we found that we had to audit some models in order to keep the track of the changes. We made a research looking for options (you know, plugins, gems, etc.) that successfully accomplish the task. We found a few ones, but eventually we came to a dead end when those tools were not fitting our expectations, they were either too complicated or too rough for our necessity. So, we decided to create a new solution named [auditrail](https://github.com/crowdint/auditrail).

Auditrail is a gem that provides a highly configurable solution to audit your models. It provides two generators. The first creates the Audit model with a couple of methods that helps you to use it, and the second creates the migration needed.

# How to use it?

The use is quite simple. You just need to install the gem *auditrail* and then run the generators provided (*auditrail:migrations* and *auditrail:model*). Then you just need to call the method *auditable* into your models. And that's it! This explanation shows the basic operation.

# How it works?

Auditrail was build to make it easily readable. So if you take a quick look at the source code you can identify all the components. Even tough, I would like to mention the basic components that do the trick.

* Auditrail keep the track of the changes of the audited models trough the model Audit. Here, you can configure your own methods to manipulate those changes. You can place there your filters or special actions.

* Auditrail is a module that extends the *ActiveRecord::Base*, this way you can use it every time just calling the *auditable* method. Simple, isn't it?

* In order to manipulate the plugin requests auditrail uses a basic DSL that handle the method calls. Those methods where built to receipt an identify if you want to audit your model only for a certain array of attributes (as symbol) and/or include the actor that is making the changes to the audited model.

* It extends the ActiveSupport::Concern to facilitate the manipulation of the instance methods and the class methods in the same module, avoiding the necessity to include an extend for the ActiveRecord::Base. Example:

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

* Auditrail uses the ActiveRecord *callbacks* to trigger the methods that help us to audit our models.

* Once auditrail knows when a model changes this will track the changes using the *ActiveModel::Dirty*. The ActiveModel::Dirty returns a hash that we serialize in order to save them in our Audit model. This changes will be stored into the attribute field *dumped_changes*. *The dumped_changes by default is generated type **string**, in case you're planing to track many changes at the same time you could change this type to **text**.

Here you can find a list with the attributes the model Audit contains:

{% highlight ruby %}
==============  ====      ===========================================================================
Attribute       Type      Usage
--------------  ----      ---------------------------------------------------------------------------
:dumped_changes  text      Keep the changes of the model serialized.
:action          string    Indicates whether was creation ("creating") or an update ("updating").
:model_changed   string    Indicates the model that changed.
:element_id      integer   Indicates the element from the audited model that has changed.
:invoker         string    Optional, you can chose if you want to store the action invoker (user).
:timestamps                You can use the timestamps to determine when the audited model has changed.
==============  =======   ===========================================================================
{% endhighlight %}

Please feel free to participate with this plugin. We want to enhance it!
