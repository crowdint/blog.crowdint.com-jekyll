---
layout: post
title: Rack Introduction
author: Emmanuel Delgado
email: emmanuel.delgado@crowdint.com
avatar: a302e7dd208f335dc67761a6db911561
published: true
---

# Implementing Rack #
Rack provides a minimal interface between web servers supporting **Ruby**. It contains a full 
stack of middleware components. Using **Rack** middleware you can build standalone applications that 
can be plugged in Rails, Sinatra and many other *Rack based frameworks*.

Rack is one of those "bare minimum components" that you need for creating modular web 
applications, furthermore, by learning Rack you begin mastering part of the *Rails internals*, why?, 
because of **Rails** has adopted the *Rack philosophy* throughout its framework, a Rails application is actually 
*a collection of Rack and Rails middleware components* that all work together to form the completed 
whole.

In this article I'll guide you through the process of installing Rack, letting you know what every rack 
application must implement/provide and creating a rack application, in the process I'll explain some 
concepts with a design pattern interpretation of rack and finally we'll see an integrating example
of the whole story.

Let's begin.

## Installing Rack ##
Just install the rack gem, as simple as:

{% highlight ruby %}
  gem install rack
{% endhighlight  %}


## Basic requirements ##
A Rack application is a **Ruby object** that **respond_to? 'call'**, it takes exactly *one argument*: 
**the environment**, and *returns* an **Array** of exactly **three values**: **http status**, **http 
headers**, and **http body**.

The headers and body returned by call have to **respond_to? 'each'**.

So basically anything like the following may act as a rack application:

{% highlight ruby %}
  status, headers, body = rack_app.call environment
{% endhighlight %}

Of course, there are more things that you need to check in order to make your application 
**Rack compliant**, but we'll get to that later.

Now we are going to build a classic example application.


## Hello World example ##
In this example, it returns a three element Array, array's elements in order are: 200 HTTP 
successful response code, a text/html content type and the HTTP response body with a "Hello 
World" message.

Open a file, call it *hello_world.ru*, and write following:

{% highlight ruby %}
  app = lambda do |env| 
    [200, 
      { 'Content-Type' => 'text/html' }, 
     'Hello World']
  end 
  run app 
{% endhighlight %}

In order to run it, I'll first send the *rackup hello_world.ru* to background, next using *curl*
we'll send a request to localhost:

{% highlight bash %}
  ..(master) $ rackup hello_world.ru &
  [1] 12508
  ...(master) $ curl localhost:9292
  127.0.0.1 - - [05/Nov/2010 12:07:54] "GET / HTTP/1.1" 200 - 0.0008
  Hello World
{% endhighlight %}

And there we have our **Hello World** message from the *http body* example.


## Basic API ##
Before going on to the next examples, you need to know that rack applications usually call
following methods:

* *use(middleware, **args, &block)* adds a middleware to the stack
* *run(app)* dispatches to an application
* *map(path, &block)* constructs a Rack::URLMap in a convenient way

Next, I'll describe a rack basic concept and after that I'll show an example on how to stack 
many rack applications.


## Middleware ##
A middleware is a Rack application that is *designed* to *run* in *conjunction* with another Rack 
application that acts as the *endpoint*.

Think of a Rack middleware as a filter *receiving* the Rack environment for the request 
from the previous *middleware, then doing some work with or on the *request's environment*
and then *calling* the *next middleware* in the chain. The *last* Rack application in the 
chain is the *application itself*, *any* middleware in the chain can *return the Rack response 
itself*, thus *preventing* the rest of the middlewares in the chain from *executing*.

Now let's build and chain three rack applications/middlewares, one *lambda endpoint* and two *call* 
responders: SayHi and SayNothing. After this example we'll go through how Rack integrates a 
*design pattern*.


## Chaining Rack applications example ##
Our endpoint application is just like the previous Hello World example but it returns an 
"I'm an endpoint" body.

The point here is to show how three different objects (middlewares) are chained, classes
*SayHi* and *SayNothing* will simply append a message to the response body, in the end, the returned 
request body will contain the message added by each middleware.

In order to avoid code repetition we'll define the common code that is inside the Initializer module
and then we'll mix it in each class using **Module#include**.

{% highlight ruby %}
  module Initializer
    def initialize(app)
      @app = app 
    end 
    def call(env)
      status, headers, body = @app.call(env)
      body << "\nHi from #{self.class}"
      [status, headers, body]
    end 
  end

  class SayHi
    include Initializer
  end

  class SayNothing
    include Initializer
  end
    
  use SayHi
  use SayNothing
  run lambda { |env| [200, {'Content-Type' => 'text/html'}, ["I'm the endpoint"]] }
{% endhighlight %}

Let's test it using *rackup*:

{% highlight bash %}
...(master) $ rackup  simple_stacked.ru  -p 1111 &
[1] 19216

...(master) $ curl localhost:1111
I'm the endpoint
Hi from SayNothing
Hi from SayHi...(master) $ 
{% endhighlight %}

The response shows the endpoint's message on the first line, plus the SayNothing response, 
plus the SayHi response. As you can see all the applications interacted directly with the
request and its response.

## Decorator pattern ##
It is important to note that Rack applications follow a decorator pattern, where each application 
receives an *Array of three elements and returns an Array of three elements*, this Array must follow
the specification we discussed above.

This pattern allows components to receive/add behavior dynamically using a common interface. If 
you want to learn more about the subject I recommend you go through [Luke Redpath's excellent
article](http://lukeredpath.co.uk/blog/decorator-pattern-with-ruby-in-8-lines.html).

The following gist intends to show a very basic decorator implementation **MyBaseApp**
that initializes with a three elements array mimicking a Rack Application, that application will be
decorated with HTML and JSON outputs:

<script src="https://gist.github.com/668084.js"> </script>

Given the dynamic nature of the Ruby language there are many ways to implement a decorator, you decide 
which one fits your needs.


## Creating valid Rack applications ##
Every Rack application must follow the [Rack Spec](http://rack.rubyforge.org/doc/SPEC.html),
Rack helps you to follow it by proving a **Rack::Lint** middleware, you should include it in your 
application in order to make it **Rack complain**. By running your application using **rackup** 
command line tool, you are already using it, otherwise include it with a:

{% highlight ruby %}
  use Rack::Lint
{% endhighlight %}

Also, you may test your application with [RSpec](http://relishapp.com/rspec), the following is an
example I'm working on based on the Rack Spec:

<script src="https://gist.github.com/668111.js"> </script>

Now, let's implement and tie everything together with the next example:

## Static file server with Haml support example  ##
Imagine your application returns files from a certain folder, just like a file server, but, 
whenever these files have a [Haml](http://haml-lang.com/) extension they will be parsed and 
rendered as html.

We'll do this in two steps, first we'll serve static files, second we'll parse haml files. Let's
start!.

### Step 1. Serving static files ###
Create a *file_server.rb* file, and put following code in it: 

{% highlight ruby %}
  require 'rack/file'
  run Rack::File.new(File.expand_path('public'))
{% endhighlight %}

Now create a *public* folder and a *index.html* file inside and fill in this html file with:

{% highlight html %}
  <html>
    <head>
      <title>The index file</title>
    </head>
    <body>
      The body
    </body>
  </html>
{% endhighlight %}

Now let's test it using *rackup* and *curl*:

{% highlight bash %}
  ...(master) $ rackup file_server.ru -p 1111 &
  [2] 17882

  ...(master) $ curl localhost:1111
  File not found: /
  ...(master) $ curl localhost:1111/index.html
  <html>
    <head>
      <title>The index file</title>
    </head>
    <body>
      The body
    </body>
  </html>
{% endhighlight %}

It works, now we have a static files server.

### Step 2. Serve haml files as html ###

{% highlight ruby %}
  require 'rack/file'

  class MyHaml
    require 'haml'
    def initialize(app)
      @app = app
    end

    def call(env)
      status, headers, body = @app.call(env)

      if env['PATH_INFO'] =~ /\.haml$/
        body = parse_haml(body)
        headers['Content-Length'] = body.length.to_s
        headers['Content-Type'] = 'text/html'
      end

      [status, headers, body]
    end

    private
    def parse_haml(body)
      full_body = traverse_body(body)
      engine = Haml::Engine.new full_body
      engine.render
    end

    def traverse_body(body)
      text = ''
      body.each {|x| text << x}
      text
    end

  end

  use MyHaml
  run Rack::File.new(File.expand_path('public'))
{% endhighlight %}

Our call method basically looks for request paths where the file name has a haml extension,
we have to change the content length and type to match what the response is returning,
here we are using *traverse_body* method just to read through the previous response body, 
since it is a *Rack::File* instance.

In order to test this, create an *index.haml* file inside public folder and fill it in with
following:

{% highlight html %}
  !!!
  %html
    %head
      %title The index from a haml file
    %body
      The haml body
{% endhighlight %}

Let's test it with *rackup* and *curl*:

{% highlight bash %}
  ...(master) $ rackup file_server.ru -p 1111 &
  [2] 19132

  ...(master) $ curl  localhost:1111/index.haml
  <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
  <html>
    <head>
      <title>The index from a haml file</title>
    </head>
    <body>
      The haml body
    </body>
  </html>
{% endhighlight %}

## Conclusion ##
There's lot more to learn about, for example :

* Ruby and Rails integration
* Ruby and Rails rack architecture
* Lots of other Rack utilities

I'll go through these examples in future articles, for now I think this is enough. 
Feel free to send me your comments, additions, resources or complaints. I will certainly
look forward to them.

Thanks for your reading and remember, others sources codes are sometimes your best teachers.

Regards
