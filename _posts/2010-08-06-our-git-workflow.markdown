---
layout: post
title: Our git workflow
author: David Padilla
email: david@crowdint.com
avatar: d32b52ec6403614b1adf3e648cbbe584
---

Using git to track changes on your source code is an easy task, a branch here, a merge there, no big deal. But, when you add different teams and features to the mix, things can get a little tricky. 

Here is how we do git, hoping this explanation helps someone understand *the power of git*.

Let's say we have our first commit A.

![A](images/2010/08/09/a.jpg)

Now, we're developing Feature 1, and one of our teammates is developing Feature 2. At this point, we'd like to create a branch per feature.

{% highlight bash %}
git branch feature1
git branch feature2
{% endhighlight %}

Now, Team Feature 1 works hard and commits B and C.

Now, our tree looks a bit like this:

![B](images/2010/08/09/b.jpg)

Team Feature 2 catches up and creates commit D

![C](images/2010/08/09/c.jpg)

Once a feature has been tested and approved for production, we merge it into master. If Feature 2 was approved first:

{% highlight bash %}
git checkout master
git merge feature2

Updating 04511b4..ed6d1fb
Fast-forward

{% endhighlight %}

![D](images/2010/08/09/d.jpg)

Master was unchanged since the time the feature2 branch was created, so, what happens here is called a fast-forward, there's no merge or rebase involved, master simply now points to D.

Once Feature 1 is approved, we want to merge it into master too. We can't do a Fast-Forward here because master now has Feature 2 in it, so, the first thing to do is merge *master into feature1*.

{% highlight bash %}
git checkout feature1
git merge master
{% endhighlight %}

![E](images/2010/08/09/e.jpg)

Test, Test, Test. In case we find a bug, or a feature incompatibility, we know the problem exists only on our feature branch, master remains stable.

After we're sure everything is going to be all right, we now merge *feature1 into master*.

{% highlight bash %}
git checkout master
git merge feature1

Updating ed6d1fb..359c6cf
Fast-forward

{% endhighlight %}

![F](images/2010/08/09/f.jpg)

Since no changes where required, once again, master is Fast-Forwarded. Now, it is the Feature2 team's responsibility to update their branch with the recent updates we did on master.

{% highlight bash %}
git checkout feature2
git merge master

Updating ed6d1fb..359c6cf
Fast-forward

{% endhighlight %}

Now, all of the branches are at the same level and both teams are ready to start working on new features.

![H](images/2010/08/09/h.jpg)

And that's basically how we use git, on a small scale. In real life we do this for 4 - 5 features being developed at the same time by teams of 2 - 4 people, but we follow the same workflow.

Cheers! 