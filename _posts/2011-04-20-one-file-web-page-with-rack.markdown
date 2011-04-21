---
layout: post
title: One File Web Page with Rack
author: Emmanuel Delgado
email: emmanuel.delgado@crowdint.com
avatar: a302e7dd208f335dc67761a6db911561
published: true
---

There are times when you need to quickly create small **Ruby** web
applications. You can do that in many different ways like, creating a
**Rails**, **Sinatra** or **Rack** application.

I like **Rack**, it gives you more than enough tools to build a web page, and
let's you have full control of the components that you employ. 

In this article I'll show some examples about how to create simple one page
*Rack* web applications.

For a bigger overview about **Rack** look at this article: 
[Rack Basics - A Rack Introduction](http://blog.crowdint.com/2010/11/17/rack-basics-a-rack-introduction.html).

Also remember, a middleware is nothing more than another **Rack**
application, and, **Rack** middlewares can be stacked up in order to compose
bigger systems.

Let's start by building a simple hello world example, save the following
code as *hello.ru*:

{% highlight ruby %}
  message = "Hello world"
  run lambda {[200, {"Content-length: #{message.length}"}, [message]]}
{% endhighlight  %}

Now, run the *hello.ru* app with:

{% highlight bash %}
  rackup hello.ru
{% endhighlight  %}

Creating an application in this way, requires you to manually specify the
headers, just as I did when added the *Content-length header*, also, this 
can be done in a simpler way by using the **Rack::ContentLength** middleware:

{% highlight ruby %}
  message = "Hello world"
  use Rack::ContentLength
  run lambda {[200, {}, [message]]}
{% endhighlight  %}

Also, you can add a default content type header using the
**Rack::ContentType** middleware:

{% highlight ruby %}
  message = "Hello world"
  use Rack::ContentLength
  use Rack::ContentType
  run lambda {[200, {}, [message]]}
{% endhighlight  %}

OK, now let's add ERB rendering to this example. I'll use this technique 
[useless ruby tricks DATA and END](http://shifteleven.com/articles/2009/02/09/useless-ruby-tricks-data-and-__end__)
in order to embed our HTML template inside the same file.

{% highlight ruby %}
  require 'erb'

  def render_view
    ERB.new(DATA.readlines.join).result
  end

  use Rack::ContentLength
  use Rack::ContentType
  run lambda {[200, {}, [render_view]]}

  __FILE__
  Hello World
{% endhighlight  %}

Now, run the script again. You should see the *Hello World* message.

In order to show one last example let's write our view using
**HAML**, and passing variables to the view:

{% highlight ruby %}
  require 'haml'

  def render_view
    Haml::Engine.new(DATA.readlines.join).render Object.new, 
      {:message => 'Hello world', :title => 'A hello world page'}
  end

  use Rack::ContentLength
  use Rack::ContentType
  run lambda {[200, {}, [render_view]]}

  __FILE__
  !!!
  %html
    %head
      %title= title
    %body
      = message
{% endhighlight  %}

Run the script again. You should see the *Hello World* message.

That's it, you've seen how to create web pages by simply using
**Rack**, no frameworks, just **Rack**.

Thank you for your reading.
