---
layout: post
title: Metaprogramming Basics - Enter the Ghost class
author: Emmanuel Delgado
email: emmanuel.delgado@crowdint.com
avatar: a302e7dd208f335dc67761a6db911561
published: false
---

For me at least, diving into **Ruby Metaprogramming** has been a totally refreshing experience,
some times it feels like a rollercoaster, some times you feel like you are finally grasping the 
whole concept, but then something weird happens that make you realize hat you are still 
learning.

This article is about a few basic topics that in my opinion *every Ruby programmer* should know in order 
to fully enter the **Ruby Metaprogramming** world.

Let's begin by listing these topics:

* self
* Ruby method call resolution
* **Ghost classes* (metaclasses, eigenclasses, singleton classes)
* **instance_eval**
* **class_eval**

At the of this article I want you to feel more confortable when navigation the Ruby objects hierarchy,
finding on what objects are different methods defined, and deciding when to evaluate code using
*instance_eval* or *class_eval*.



## Self ##
Following are some assertions that everybody talking or speaking about metaprogramming does,
so I'll do my part by adding my own notes and examples:


### #1 Self - The explicit receiver for method calls ###
When you invoke a method (also known as sending a meesage), this method has to be invoked
on a **object** (I bet that you've already heard that in Ruby everything is an object right?),
and that's pretty clear when you have an **explicit receiver** like so:

<script src="https://gist.github.com/702312.js?file=explicit_greeter.rb"></script>

* At line 9, the **explicit receiver** is the *greeter* object, it receives a *greet* message.
* At line 13, the **explicit receiver** is the *person* object, it receives a *swapcase* message.
* At line 17, the **explicit receiver** is the *date* object, it receives a *day*  message.

As you see there is the **some_object** **dot** **notation**, where **some_object** receives
the method call (or message).

So far there's nothing new, just redundance (intentionally), wait..., what happens when there's no
**some_object** **dot** notation?


### #2 Self- The implicit receiver for method calls ###
Method calls are all around ruby, lots of sent messages and method invocations, lots of
**implicit receiver** method invocations I would say, for example:

<script src="https://gist.github.com/702312.js?file=implicit_greeter.rb"></script>

* At line 3, the **implicit receiver** is the *main* object, it receives a *p* message. Note 
  that *main* is a tweaked instance of Object.
* At line 7, the **implicit receiver** is, again, the *main* object, it receives a *greet* message.
* At line 12, the **implicit receiver** will be, the *greet* object created a few lines below, it 
  receives a *greet* message.
* At line 16, the **implicit receiver** will be, the *greet* object created a few lines below, it 
  receives a *p* message. As you might have imaginated, *p* is defined at [Kernel](http://www.ruby-doc.org/core-1.8.7/classes/Kernel.html#M001112),
  there's a good explanation about how this happens at [stack overflow](http://stackoverflow.com/questions/1758284/what-is-p-in-ruby).

Basically, the **implicit receiver** is determined by the context in which a method is called. It
may happen that this is still not clear, but keep reading, I promise that after following examples
and the third point (where we talk more about *self*) everything will be clear.


### Examples ###
You may ask: how can you be that sure about *who self is*?, if so then test it by your self:

<script src="https://gist.github.com/702312.js?file=explicit_greeter_self_output.rb.rb"></script>
<script src="https://gist.github.com/702312.js?file=implicit_greeter_self_output.rb.rb"></script>


### #3 Seld - The current method receiver ###
Wether it is **implicit** or **explicit** all method calls/messaged are sent through **self**, and here
comes another lesson.

### #4 Self - Changes in two ways  ###

#### Self changes because of method calls ####
Self being the default method receiver implies that it can not always be the object *main* instance of
**Object**, it has to be other objects, you name it, *Strings, Integer, CustomObjects, Views* etc.

#### Self changes because of classes and module definitions ####







## Conclusion ##
You might have noticed that I'm not saying nothing new or that nobody else has said before, in fact most of what
I'm about to say are things that I learnt at the [rubylearning.org Metaprogramming course](http://rubylearning.org),
at some Dave Thomas talks and Paolo Perrota's book.

