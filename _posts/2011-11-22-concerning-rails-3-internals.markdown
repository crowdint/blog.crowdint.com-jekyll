---
layout: post
title: Concerning Rails 3 Internals
author: Mumo Carlos
email: mumoc@crowdint.com
avatar: 89c6420fb99cb01bcf2e3502a7851da2
---

As you may know, when we want to increase the functionallity of a class, we usually 'include' or 'extend' it through modules which includes the methods.We use 'include' to add Instance Methods and 'extend' for the Class Methods.

With this in mind, we usually see things like:

{% highlight ruby %}
# lib/posts_lib.rb
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

Looking at the code, we could notice we're overwriting the method 'self.included()', which is a especial callback called when the module is included ('include PostsLib'  < Right Here).

Note, we used base.send : extend instead :extend alone to have access to private methods.

Just to clarify, this code will let us doing things like:

{% highlight ruby %}
@post.post_tags              #instance method
Post.find_by_author 'Mumo'   #class method
{% endhighlight %}

(I know, i know, but this is an example)

With ActiveSupport::Concern we could do it this way:
 
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
{% endhighlisght %}

With this, we obtain a clearer code and, for example, sometimes you want to add other code in the self.included() such logging:

{% highlight ruby %}  
def self.included base
  logger.warn('Something goes here xD')
end
{% highlisght%}

Well, that's all, i'm still testing and learning, but i hope this will be usefull in some way. ^^

Info source:
  [Better Ruby Idioms](http://yehudakatz.com/2009/11/12/better-ruby-idioms/)
  [ActiveSupport Concern](http://api.rubyonrails.org/classes/ActiveSupport/Concern.html)
  [Google](http://www.google.com)
  
