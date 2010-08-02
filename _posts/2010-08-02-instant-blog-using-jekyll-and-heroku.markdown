---
layout: post
title: Instant blog using Jekyll and Heroku
author: David Padilla
email: david@crowdint.com
avatar: d32b52ec6403614b1adf3e648cbbe584
---

When we were trying to put this blog online, we checked out different options, until we stumbled upon [Jekyll](http://wiki.github.com/mojombo/jekyll/).

It was geeky enough for us, so we decided to try it out, and now, I am very happy that we decided to go with it.

We needed to put it online somewhere, and since Heroku is such an awesome, not to mention free, platform, we decided to host there.

Now, I'll make a brief explanation on how we did this. I won't get into the details on how to use Jekyll, just a brief intro.

You can find more info on how to use this awesome content management tool on their [Wiki](http://wiki.github.com/mojombo/jekyll/).

## Setup jekyll

The first thing you have to do is, install the jekyll gem. Follow the instructions [here](http://wiki.github.com/mojombo/jekyll/install), but, basically
all you have to do is run:

{% highlight bash %}
gem install jekyll
{% endhighlight %}

## Create the site structure

Create a folder wherever you want to put your blog files.

{% highlight bash %}
mkdir my_blog
{% endhighlight %}

and create the following files:

{% highlight bash %}
cd my_blog
touch _config.yml
touch index.html
mkdir _posts
mkdir _layouts
touch _layouts/default.html
{% endhighlight %}

For more info on what those files do, or what should you put on them, take a look [here](http://wiki.github.com/mojombo/jekyll/usage), or checkout [this huge list](http://wiki.github.com/mojombo/jekyll/sites) of working examples.

## "Hello World" Jekyll

### _layouts/default.html

First, let's create a Layout, open *_layouts/default.html* and put the following code on it:

{% highlight html %}
<html>
<body>
  <h1>Hello World!</h1>
</body>
</html>
{% endhighlight %}

Now, we need an index page. Open *index.html* and add the following code at the top:

{% highlight html %}
---
layout: default
---
{% endhighlight %}

What we're telling the index page is to use the default layout we created earlier.

Run:

{% highlight bash %}
jekyll--server --auto
{% endhighlight %}

Open your browser and go to http://127.0.0.1:4000

You should now see "Hello World"

## Deploying to Heroku

Now, let's try and deploy our Hello World blog to Heroku. There is a gem called [rack-jekyll](http://github.com/bry4n/rack-jekyll) that makes this very easy. 

You can read the documentation on the gem page, the basic steps are:

First, install the Heroku gem

{% highlight bash %}
gem install heroku
{% endhighlight %}

Create a config.ru file:

{% highlight ruby %}
require "rack/jekyll"

run Rack::Jekyll.new
{% endhighlight %}

Add the rack-jekyll gem to the heroku manifest:

{% highlight bash %}
echo "rack-jekyll" > .gems
{% endhighlight %}

Create a git repo and an initial commit:

{% highlight bash %}
git init
git add .
git commit -m "First commit"
{% endhighlight %}

Create a Heroku app:
{% highlight bash %}
heroku create
{% endhighlight %}

And, deploy!

{% highlight bash %}
git push heroku master
{% endhighlight %}

Check out how it looks:

{% highlight bash %}
heroku open
{% endhighlight %}

This is just the basic stuff on Jekyll, make sure you go through the source code of [other blogs](http://wiki.github.com/mojombo/jekyll/sites) to get more tips on advanced stuff like archives, comments, includes, links to posts...

Cheers!