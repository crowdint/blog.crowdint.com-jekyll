---
layout: post
title: What I would've love to know when I first met Ruby
author: Emmanuel Delgado
email: emmanuel.delgado@crowdint.com
avatar: a302e7dd208f335dc67761a6db911561
published: true
---

Motivation
----------

Everytime I'm learning a new language, the first thing I try to learn is how this new language
implements OOP, and now its Ruby's turn. Before joining Crowd Interactive I had been playing with Java for some
years, but when I first heard about Ruby being a fully OOP language I got really excited, but then
when I got hands-on on some existing Ruby code thing got confusing.

I would've liked to know all of this before looking at any Ruby code:

First things first: Ruby Objects
-------------------------------
Things that we have to know:

* And object is formed by: State + Behavior = Object
* Everything in Ruby is an object, even nil which is *NilClass*, false *FalseClass*, true *TrueClass* and modules

{% highlight ruby %}
ruby-1.8.7-p299 > "some".class
 => String 
ruby-1.8.7-p299 > "some".class.superclass
 => Object 
ruby-1.8.7-p299 > "some".class.superclass.superclass
 => nil 
ruby-1.8.7-p299 > "some".class.superclass.superclass.class
 => NilClass 
ruby-1.8.7-p299 > false.class
 => FalseClass 
ruby-1.8.7-p299 > true.class
 => TrueClass 
ruby-1.8.7-p299 > module B end
 => nil 
ruby-1.8.7-p299 > B.class
 => Module 
ruby-1.8.7-p299 > B.class.superclass
 => Object 
{% endhighlight %}

* Do you remember those called static classes in languages other than Ruby? They're objects too, 
and when I say objects I really mean it. Consider following stored in test.rb file:

{% highlight ruby %}
class A
  def self.outside
    self
  end

  def inside
    self
  end 
end
{% endhighlight %}
 
Let's play with it using irb:
{% highlight ruby %}
ruby-1.8.7-p299 > require './test'
ruby-1.8.7-p299 > A.outside
 => A 
ruby-1.8.7-p299 > A.outside.class
 => Class 
ruby-1.8.7-p299 > A.new.inside
 => #<A:0x7ffc94046f58> 
ruby-1.8.7-p299 > A.new.inside.class
 => A 
{% endhighlight %}

As you can see both are *A* objects whose type is *Class*

* Those class names are constants containing references to objects:

{% highlight ruby %}
ruby-1.8.7-p299 > clazz = Class.new
 => #<Class:0x7f8fdec83230> 
ruby-1.8.7-p299 > clazz.class
 => Class 
ruby-1.8.7-p299 > Blabla = clazz
 => Blabla 
ruby-1.8.7-p299 > Blabla.class
 => Class 
ruby-1.8.7-p299 > Blabla.new
 => #<Blabla:0x7f8fdec77a70> 
{% endhighlight %}

Self
----
When I first met Ruby I thought "self" was the same as *this* (like in many other languages), and I was wrong, so lets 
see what it is:

#### Default receiver of method calls 
Since everything in Ruby are objects, then all the function definitions we find defined all around 
and all those functions we call like [p](http://ruby-doc.org/core/classes/Kernel.html#M005961), 
[puts](http://ruby-doc.org/core/classes/Kernel.html#M005954), def, etc are method calls. Some may 
not be exactly defined inside a class but, in the end they will get either inherited or mixed in.

Look at following example:
{% highlight ruby %}
p "1. Current receiver is #{self}"
class A
  p "2. Current receiver is #{self}"
  def initialize
    p "3. Current receiver is #{self}"
  end 
  p "4. Current receiver is #{self}"
end
p "5. Current receiver is #{self}"
{% endhighlight %}

Running this script we see:
{% highlight bash %}
user@user-desktop:~$ ruby test.rb 
"1. Current receiver is main"
"2. Current receiver is A"
"4. Current receiver is A"
"5. Current receiver is main"
{% endhighlight %}

#### Where instance variables are found
{% highlight ruby %}
class A
  def initialize
    @a, @b = 2, 1
  end
end

p A.new.instance_variables
{% endhighlight %}

Running this script we see:
{% highlight bash %}
user@user-desktop:~$ ruby test.rb 
["@b", "@a"]
{% endhighlight %}

Metaprogramming
------------------------------
This is why I fell in love with ruby, it is a huge topic which I won't cover, but it allows you
to write code that writes code lets see some examples:

#### Create a *A Class*, re-open it and see how it is the same object
{% highlight ruby %}
class A
  def method1
    p "I'm method one"
  end
end

p A.instance_methods false
p A.object_id

class A
  def method2
    p "I'm method two"
  end
end

p A.instance_methods false
p A.object_id
{% endhighlight %}

Which throws:
{% highlight bash %}
user@user-desktop:~$ ruby test.rb 
["method1"]
70312385439840
["method2", "method1"]
70312385439840
{% endhighlight %}

As you saw the object id remains the same after re-open a class in order to add a second method, also, 
we got all instance methods that one [class](http://ruby-doc.org/core/classes/Object.html#M000350) 
of *A* would have.

#### Call all instance methods of a certain class whenever you call a method whose name matches
{% highlight ruby %}
class A
  def hello
    p "Hello"
  end 
  def bye 
    p "Bye"
  end 
  def welcome
    p 'Welcome'
  end 
  def method_missing(method_name, *args, &block)
    method_to_call = methods.find {|name| name =~ /#{method_name.to_s}/}
    send(method_to_call) if method_to_call
    p 'Unknown method' unless method_to_call
  end 
end

instance = A.new
instance.els
instance.hel
instance.by
instance.wel
{% endhighlight %}

Which throws:
{% highlight bash %}
user@user-desktop:~$ ruby test.rb 
"Unknown method"
"Hello"
"Bye"
"Welcome"
{% endhighlight %}

That's it I hope this gives you some good overview and if you are ready for the whole package I 
suggest you start with:

* Some good metaprogramming screencasts [Pragmatic Programmers screencasts](http://pragprog.com/screencasts/v-dtrubyom/the-ruby-object-model-and-metaprogramming)
* An in deep look at [Rafa Maga&ntilde;a's presentation](http://raflabs.com/blogs/silence-is-foo/2009/12/13/the-ruby-object-model/) on Ruby object model 

Happy programming!
