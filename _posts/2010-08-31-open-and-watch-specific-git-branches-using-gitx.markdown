---
layout: post
title: Open and watch specific git branches using gitx
author: David Padilla
email: david@crowdint.com
avatar: d32b52ec6403614b1adf3e648cbbe584
published: false
---

It happens, the project and the team grows, and now, there's a bunch of branches in your git repo. It's called *branchitis*, and it happens to everyone at some point.

So, you open gitx, and all you see is a bowl of spaghetti

![Bowl of Spaghetti](/images/2010/08/31/a.jpg)

So, if you want to see a specific branch, you can call gitx from the command line and specify the branch you want to see

{% highlight bash %}
$ gitx feature2
{% endhighlight %}


![One branch](/images/2010/08/31/b.jpg)

Nothing out of this world here, since you can do the same thing by using the built in dropdown on gitx to select the branch you want to see, but, did you know that you can specify more than one branch?

So, let's say you want to merge the *feature2* and the *feature5* branch, but first, you want to see how the branches are related. You can call gitx from the command line and specify *both* branches as the arguments.

{% highlight bash %}
$ gitx feature2 feature5
{% endhighlight %}

Then you'd get something like this:

![Two branches](/images/2010/08/31/c.jpg)

Cool! You can actually read this first hand, and it's faster than trying to descipher the spaguhetti.

In case you're wondering, yes, you can specify 3 branches, or as many as you'd like to see. Go ahead, try it out.

Hope this helps you in one way,

Cheers.