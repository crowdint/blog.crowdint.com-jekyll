---
layout: post
title: Concerning Rails 3 Internals
author: Mumo Carlos
email: mumoc@crowdint.com
avatar: 89c6420fb99cb01bcf2e3502a7851da2
---

As you may know, when we want to enhance the functionality of a class, we usually
'include' or 'extend' it through modules which include new methods or override
existing ones.

We use 'include' to add Instance Methods and 'extend' for Class Methods.

With this in mind, we usually see things like:

{% highlight ruby %}
#lib/posts_lib.rb

module PostsLib
  module ClassMethods
    def find_by_author(author)
      #...
    end
  end

  module InstanceMethods
    def post_tags()
      #...
    end
  end

  def self.included base
    base.send :include, InstanceMethods
    base.send :extend, ClassMethods
  end
end

class Post < ActiveRecord::Base
  include PostsLib
end
{% endhighlight %}

Looking at the code, we notice that we're overwriting the
'self.included()' method, which is a especial callback that is executed when the module
is included on another class ('include PostsLib'  < Right Here).

Note, we need to use base.send :extend instead because extend is a
private method.

Just to clarify, this code will let us do things like:

{% highlight ruby %}
@post.post_tags              #instance method
Post.find_by_author 'Mumo'   #class method
{% endhighlight %}

ActiveSupport::Concern gets rid of the self.included override altogether:

{% highlight ruby %}
#lib/posts_lib.rb
module PostsLib
  extend ActiveSupport::Concern
  module ClassMethods
    def find_by_author(autho)
      #...
    end
  end

  module InstanceMethods
    def post_tags()
      #...
    end
  end
end
{% endhighlight %}

With this, we obtain clearer code and, for example, sometimes you want to add
different code to self.included() such as logging:

{% highlight ruby %}

def self.included base
  logger.warn('Something to log here')
end

{% endhighlight%}

Well, that's all, I'm still testing and learning, but i hope this will be useful to you in some way. ^^

Info source:
  [Better Ruby Idioms](http://yehudakatz.com/2009/11/12/better-ruby-idioms/)
  [ActiveSupport Concern](http://api.rubyonrails.org/classes/ActiveSupport/Concern.html)
  [Google](http://www.google.com)

