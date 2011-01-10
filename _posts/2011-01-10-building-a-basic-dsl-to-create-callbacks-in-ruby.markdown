---
layout: post
title: Building a basic DSL to create callbacks in Ruby
author: Emmanuel Delgado
email: emmanuel.delgado@crowdint.com
avatar: a302e7dd208f335dc67761a6db911561
published: true
---

Callbacks are useful when you want to perform certain actions *around* existing
objects, one example is [ActiveRecord Callbacks](http://api.rubyonrails.org/classes/ActiveRecord/Callbacks.html)

In this article we are going to build **Wrappable**, a simple custom DSL to 
*wrap* a method with *callbacks* using the Ruby language.


## What is a DSL? ##
[Wikipedia says](http://en.wikipedia.org/wiki/Domain-specific_language):

> In software development and domain engineering, a domain-specific 
> language (DSL) is a programming language or specification language dedicated
> to a particular problem domain, a particular problem representation
> technique, and/or a particular solution technique.


## What is a callback? ##
[Wikipedia says](http://en.wikipedia.org/wiki/Callback_%28computer_programming%29):
> In computer programming, a callback is a reference to executable code, or a
> piece of executable code, that is passed as an argument to other code. This
> allows a lower-level software layer to call a subroutine (or function)
> defined in a higher-level layer.


## What are we going to do? ##
We are going to build step by step a *wrap* method that will be able to call 
the methods *before* and *after* an *original* method.


## Usage example ##
We will end up the example with the following **CallbackTest** class:

{% highlight ruby %}
    class CallbackTest

      # This module contains the whole functionality
      include Wrappable

      def original
        puts "Original method"
      end

      def before
        puts "Before method"
      end

      def after
        puts "After method"
      end

      wrap :original do
        before_run :before
        after_run :after
      end

    end

    CallbackTest.new.original
{% endhighlight %}

The script output should be as follows:

{% highlight bash %}
    ...$ ruby my_test.rb
    Before method
    Original method     
    After method
{% endhighlight %}


## Writing the callback step by step ##
In order to build the whole example let's start by listing what we require
to do and then we will code the example from scratch.


### The requirements ###
Consider the script fragment where *wrap* is invoked:

{% highlight ruby %}
    wrap :original do
      before_run :before
      after_run :after
    end
{% endhighlight %}

This means:

* **Step 1**, we need a class method called *wrap*. The *wrap* method has two
  parameters: The first is the *symbol representing the name of the method that 
  will be wrapped and the second is a block.
* **Step 2**, the block parameter contains two method calls that configure
  what methods should be invoked: *before_run* and *after_run*. Each method
  receives one parameter as symbol that represents the name of the method 
  that will be invoked respectively.
* **Step 3**, create the *wrap* behavior. This step involves creating a new
  method that will eventually call the *original*, *before* and *after* 
  methods and implies that we need to keep a reference to the *original* 
  method so we do not overwrite it.


### Step 1 ###
What we are going to do here is:
* Create a **Wrappable** module with an empty *wrap* method and its two
  parameters.
* Add the **Wrappable** module methods to the **CallbackTest** metaclass
  (adding static methods).
* Invoking the **Wrappable**'s *wrap* method inside *CallbackTest* class.

Now, save the following snippet as *callback_test.rb*:

{% highlight ruby %}

    module Wrappable
      def wrap(original_method, &block)
      end
    end

    class CallbackTest

      extend Wrappable

      def original
        puts "Original method"
      end

      wrap :original do
      end

    end

    CallbackTest.new.original
{% endhighlight %}

Now execute it: 

{% highlight bash %}
    ...$ ruby callback_test.rb
    Original method
{% endhighlight %}


### Step 2 ###
What we are going to do here is:

* Invoke the *before_run* and *after_run* inside the *wrap*'s parameter block.
* Create a **WrapperOptions** class. 
  * This class will namespace the *before_run* and *after_run* methods. 
  * I want the *wrap*'s parameter block to be evaluated inside this 
    **WrapperOptions** class.
  * By using this class we can handle all options parsing in just one place.

Update *callback_test.rb* with the following:

{% highlight ruby %}

    module Wrappable
      class WrapperOptions
        def initialize(&block)
          instance_eval(&block)
        end
        private
        def before_run(method_name)
          @before = method_name
        end
        def after_run(method_name)
          @after = method_name
        end
      end

      def wrap(original_method, &block)
        wrapper_options = WrapperOptions.new(&block)
      end
    end

    class CallbackTest

      extend Wrappable

      def original
        puts "Original method"
      end

      def before
        puts "Before method"
      end

      def after
        puts "After method"
      end

      wrap :original do
        before_run :before
        after_run :after
      end

    end

    CallbackTest.new.original
{% endhighlight %}

And verify that your script still works: 

{% highlight bash %}
    ...$ ruby callback_test.rb
    Original method
{% endhighlight %}


### Step 3 ###
Our **CallbackTest** remains unchanged. Let's improve and complete the
**Wrappable** module. What we are going to do here is:

* *Alias* the *original* method so we don't overwrite it.
* Create *accessors* to our *wrapped* method names in the **WrapperOptions**
  class.
* Create a new method that will eventually call the *original*, *before* and
  *after* methods.

{% highlight ruby %}

    module Wrappable
      class WrapperOptions
        attr_reader :before, :after
        def initialize(&block)
          instance_eval(&block)
        end
        private
        def before_run(method_name)
          @before = method_name
        end
        def after_run(method_name)
          @after = method_name
        end
      end

      def wrap(original_method, &block)
        wrapper_options = WrapperOptions.new(&block)
        alias_method :old_method, original_method
        define_method original_method do
          send(wrapper_options.before)
          send(:old_method)
          send(wrapper_options.after)
        end
      end
    end

{% endhighlight %}

And finally test that the whole this script works:

{% highlight bash %}
    ...$ ruby my_test.rb
    Before method
    Original method     
    After method
{% endhighlight %}

That's it.


## Conclusion ##
This was a simple way to build a custom DSL, there are many DSL examples all
around the web one of them is **RSpec**. 

Note that this implementation only supports method names as parameters, if 
you want to view an example of transparently supporting *blocks* as wrappers
then look at the [complete exercise gist](https://gist.github.com/762214).

If you really need to implement callbacks then I recommend you to look at the 
[**ActiveSupport::Callbacks** package](http://api.rubyonrails.org/classes/ActiveSupport/Callbacks.html).

Thank you for reading. 

Regards
