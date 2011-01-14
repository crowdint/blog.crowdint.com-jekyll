---
layout: post
title: Building a basic DSL to create callbacks in Ruby
author: Emmanuel Delgado
email: emmanuel.delgado@crowdint.com
avatar: a302e7dd208f335dc67761a6db911561
published: true
---

## Introduction ##
Do you know what a **Domain Specific Language(DSL)** is and how to 
implement one in Ruby?. This article aims to provide a slight introduction 
to this topic. It is divided in 3 sections, first we'll define what a DSL is, 
second we'll see some examples of DSL implementations, and third we'll build 
a DSL.

## What is a DSL? ##
[According wikipedia](http://en.wikipedia.org/wiki/Domain-specific_language) a 
DSL is defined as:

> In software development and domain engineering, a domain-specific 
> language (DSL) is a programming language or specification language dedicated
> to a particular problem domain, a particular problem representation
> technique, and/or a particular solution technique.

To clarify, we'll see some examples. As you read through them take into account 
the following points:

* [Ruby blocks](http://rubylearning.com/satishtalim/ruby_blocks.html) 
are used everywhere. They are the bare minimum construction element.
* Though the used structures are not part of the Ruby core, all of 
them use valid Ruby constructs. 
* The main purpose of creating new code structure is to provide a more 
*human readable code*.

This implies that the following DSL examples(codes) are build with
sentences like:

{% highlight ruby %}
  def describe(subject, &block); end

  def Given(expression, &block); end

  def get(route, &block); end
{% endhighlight %}

Be aware, its respective gems may not define each DSL as I did, but it helps 
to show different possible ways to do it.

Let's start with the examples.

## DSL implementations ##
If you are doing Ruby then you probably have already used DSL's. Gems like
RSpec, Cucumber and Sinatra are good examples of DSL implementations. Let's 
see their syntax and put special attention to the structures they use.

First, let's see three snippets from these languages. 

### Rspec snippet ###
In RSpec when you want to test if some object responds to a method call
you usually write something like:

{% highlight ruby %}
  describe MyObject do
    it 'should respond to a method call' do
      subject.should respond_to(:method_call)
    end
  end
{% endhighlight %}

### Cucumber snippet ###
In Cucumber when you write step definitions you do things like:

{% highlight ruby %}
  Given /^I click link "([^""]*)"$/ do |link|
    find(:css, link).click
  end
{% endhighlight %}

### Sinatra snippet ###
In Sinatra when you whant to write a route/controller you do something
like:

{% highlight ruby %}
  get "/" do
    "Hi there"
  end
{% endhighlight %}

Now let's write our own DSL.

## Writing a DSL ##
The following technique intention is similar to that from 
[Rails Controller Filters](http://guides.rubyonrails.org/action_controller_overview.html#filters).

Let's build a DSL called **Wrappable**. **Wrappable** will be a simple custom DSL that 
*wrap*'s' a method  with *callbacks* using the Ruby language.

Let me clarify what I mean by *wrap* using the following snippet:

{% highlight ruby %}
  def before; end
  def original; end
  def after; end
{% endhighlight %}

**Wrappable** will *wrap* the *original* method. Whenever *original* is 
invoked, the *before* method will be  automatically invoked first, second it
will invoke the *original* method and finally it will invoke the *after* 
method.

The way the *before* and *after* methods behave is known as a 
[Callback](http://en.wikipedia.org/wiki/Callback_%28computer_programming%29).

This behavior can also be achieved with something like:

{% highlight ruby %}
  def before; end
  def original
    before
    # original sentences
    after
  end
  def after; end
{% endhighlight %}

But, I want to do this dynamically, using a *wrap* method that will be 
able to setup the calls to the methods *before* and *after* programatically.

### Usage example ###
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

### Writing the callback step by step ###
In order to build the whole example let's start by listing what we require
to do and then we will code the example from scratch.


#### The requirements ####
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


#### Step 1 ####
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


#### Step 2 ####
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


#### Step 3 ####
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
around the web for example:

* At [Ron Evan's blog](http://deadprogrammersociety.blogspot.com/2006/11/ruby-domain-specific-languages-basics.html)
* At [rubylearning.org's blog](http://rubylearning.com/blog/2010/11/30/how-do-i-build-dsls-with-yield-and-instance_eval/)
* At [Obie Fernandez's blog](http://obiefernandez.com/presentations/obie_fernandez-agile_dsl_development_in_ruby.pdf)

Note that this implementation only supports method names as parameters, if 
you want to view an example of transparently supporting *blocks* as wrappers
then look at the [complete exercise gist](https://gist.github.com/762214).

Finally, if you really need to implement callbacks then I recommend you to look at the 
[**ActiveSupport::Callbacks** package](http://api.rubyonrails.org/classes/ActiveSupport/Callbacks.html).

Thank you for reading. 

Regards
