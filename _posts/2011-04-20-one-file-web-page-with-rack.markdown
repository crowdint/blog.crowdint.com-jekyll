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

## Examples source code
You can download and run the examples by following the next steps:

{% highlight bash %}
  git clone git://gist.github.com/935008.git gist-935008
  cd gist-935008
  gem install bundler && bundle install
{% endhighlight %}


## Hello world

Let's start by building a simple hello world example, save the following
code as *hello.ru*:

<script src="https://gist.github.com/935008.js?file=hello1.ru"></script>

Now, run the *hello.ru* app with:

{% highlight bash %}
  rackup hello.ru
{% endhighlight  %}

Optionally you may specify what port you want your application to run
at:

{% highlight bash %}
  rackup hello.ru -p 8080
{% endhighlight  %}

And then, go to [http://localhost:9292/](http://localhost:9292/) or 
[http://localhost:8080/](http://localhost:8080/) (depending on what port
you specified), so you can see your app alive.

Creating an application in this way, requires you to manually specify the
headers, just as I did when I added the *Content-Length* and 
*Content-Type* headers. Also, this can be accomplished in a simpler way 
by using the **Rack::ContentLength** and **Rack::ContentType** middlewares:

<script src="https://gist.github.com/935008.js?file=hello2.ru"></script>

OK, now let's add ERB rendering to this example.

<script src="https://gist.github.com/935008.js?file=hello3.ru"></script>

Now, run the script again. You should see the *Hello World* message.

In order to show one last example let's write our view using
**HAML**, and passing variables to the view:

<script src="https://gist.github.com/935008.js?file=hello4.ru"></script>

Run the script again. You should see the *Hello World* message.

That's it, you've seen how to create web pages by simply using
**Rack**, no frameworks, just **Rack**.

Thank you for your reading.
