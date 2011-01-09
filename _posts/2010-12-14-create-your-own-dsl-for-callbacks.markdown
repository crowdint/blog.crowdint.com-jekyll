---
layout: post
title: Create your own DSL for callbacks in ruby
author: Emmanuel Delgado
email: emmanuel.delgado@crowdint.com
avatar: a302e7dd208f335dc67761a6db911561
published: true
---

In this article I'll guide you through the proccess of building a custom DSL to
create callbacks for your methods in the Ruby language.

# What is a DSL? #
Wikipedia says: 
> In software development and domain engineering, a domain-specific 
> language (DSL) is a programming language or specification language dedicated
> to a particular problem domain, a particular problem representation
> technique, and/or a particular solution technique.


# What is a callback? #
Wikipedia says:
> In computer programming, a callback is a reference to executable code, or a
> piece of executable code, that is passed as an argument to other code. This
> allows a lower-level software layer to call a subroutine (or function)
> defined in a higher-level layer.


# What are we going to do? #
We will create a *wrap* method which will be able to call other methods before
and after an *original* method.

# Usage example #
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

I want the *before* and *after* methods internally invoked around the 
*original* call. The script output should be the following:

{% highlight bash %}
    ...$ ruby my_test.rb
    Before method
    Original method     
    After method
{% endhighlight %}


# Writing the callback step by step #
In order to build the whole example let's start by listing what we require
to do, after that, we'll code the example from scratch.

## The requirements ##
Consider the script fragment where *wrap* is invoked:

{% highlight ruby %}
    wrap :original do
      before_run :before
      after_run :after
    end
{% endhighlight %}

This means:

* **Step 1**, we need a class method called wrap. The *wrap* method has two
  parameters: First the symbol representing the name of the method that will
  be wrapped and second is a block.
* **Step 2**, the block parameter contains two method calls which configure
  what methods should be invoked *before_run* and *after_run*. Each method
  receives one parameter as symbol representing the name of the method will be
  invoked respectively.
* **Step 3**, create the wrap behavior. This step involves creating a new
  method that will eventually call the *original*, *before* and *after* 
  methods and implies that we need to *alias* the *original* method so it 
  don't overwrite it.

## Step 1 ##
What we are going to do is:
* Create a *Wrappable* module with an empty *wrap* method and its two
  parameters.
* Add the *Wrappable* module methods to the *CallbackTest* metaclass
  (adding static methods).
* Invoking the *Wrappable*'s *wrap* method inside *CallbackTest* class.

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

## Step 2 ##
What we are going to do is:
* Invoke *before_run* and *after_run* inside the *wrap*'s parameter block.
* Create a **WrapperOptions** class. 
  * This way we will namespace the *before_run* and *after_run* methods. 
  * I want the *wrap*'s parameter block to be evaluated inside this 
    **WrapperOptions** class.
  * By using this class we can handle all options parsing in just one place.

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

Now, verify that your script still works: 

{% highlight bash %}
    ...$ ruby callback_test.rb
    Original method
{% endhighlight %}

## Step 3 ##
Our **CallbackTest** remains unchanged, we are going to rework and complete
the **Wrappable** module. What we are going to do is:

* *Alias* the *original* method so I don't overwrite it.
* Create *accessors* to our *wrapped* method names in the **WrapperOptions**
  class.
* Create a new method that will eventually call the *original*, *before* and
  *after* methods .

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

# Conclusion #
This was a simple way to build a custom DSL, there are many DSL examples all
around the web one of them is **RSpec**. 

Note that this implementation only supports method names as parameters, if 
you want to view an example of transparently supporting *blocks* as wrappers
then look at the [complete exercise gist](https://gist.github.com/762214).

If you really need to implement callbacks then I recommend you to look at the 
[**ActiveSupport::Callbacks** package](http://api.rubyonrails.org/classes/ActiveSupport/Callbacks.html).

Thank you for reading. 

Regards
