---
layout: post
title: First Things First... Our initial git configuration
author: David Padilla
email: david@crowdint.com
avatar: d32b52ec6403614b1adf3e648cbbe584
---

Whenever a new teammate joins Crowd Interactive, one of the first things they have to do is setup their git environment.

The basic steps we recommend are:

## Let us know who you are

You can set up your email and name by using the following command:

{% highlight bash %}
$ git config --global user.name "YOURNAME"
$ git config --global user.email "YOUREMAIL"
{% endhighlight %}

## Reduce the risk factor

Well, it has happened to us before. Let's say you did something weird on your_branch and you need to force a push for some weird reason. You check everything looks ok on your branch and you push it.

{% highlight bash %}
$ git push --force
+ 013c43a...0759089 master -> master (forced update)
+ 013c43a...0759089 your_branch -> your_branch (forced update)
+ 013c43a...0759089 not_your_branch -> not_your_branch (forced update)
{% endhighlight %}

Woah, you just force updated 2 more branches than expected and now you've made quite a mess of your repo. Not good, it is bad enough that you are pushing forced updates to the repo, now you're gonna get yelled at.

So, we recommend our rookies to use the following command:

{% highlight bash %}
$ git config --global push.default current
{% endhighlight %}

What this does is configure git to push only your current branch, and nothing else. So, in the last example you would've made a smaller mess.

If you want to push a branch that is not the one you're working on, you can do so by specifying the remote and the branch name:

{% highlight bash %}
$ git push origin your_branch
{% endhighlight %}

At this point I am very used to push like that, using bash auto completion makes it very easy and it makes me feel clean.

## Add color to your life

Last but not least, who likes dull colorless console messages?, run this commands and add some color to your git outputs:

{% highlight bash %}
$ git config --global color.branch auto
$ git config --global color.diff auto
$ git config --global color.interactive auto
{% endhighlight %}

Hope this guide helps you in some way, cheers!