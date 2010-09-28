---
layout: post
title: Negative Code
author: Luis Galaviz
email: luis.galaviz@crowdint.com
avatar: 1e7f8fb8733b6193cf3bdbc85693f515
---

Last night I was checking certain social network at home, you know, the usual bad habit. When suddenly I found a phrase that catch my attention. What was that uncertain concept that caught my senses? Well, that was "negative code". "What is it?"" I thought. 

As astonished I was at that time, I took a few seconds to ask my old fellow ([G...](http://www.google.com)) "what is negative code?". Of course, as wise as always, it shows me several conversations about that interesting topic. I found many concepts trowed in the air, but nothing that I can define as a concept until I found a [this](http://stackoverflow.com/questions/3800707/what-is-negative-code) discussion at [stackoverflow](http://stackoverflow.com). Actually a user named Thilo mentioned a concept that barely filled my expectations, and here is it:

{% highlight bash %}
"...reducing lines of code, by removing redundancies or using more concise constructs".
{% endhighlight %}

Well, that minds something, isn't it? Well, yes, but at the same time I was feeling something was missing. I thought "Ok, everybody knows that if you code to write less code, but obtain a right result that code results in saving code lines! But! That isn't negative code!".

A little shocked and confused I continued reading below on the discussion. Thilo also quoted an old anecdote he found:

{% highlight bash %}
"When the Lisa team was pushing to finalize their software in 1982, project managers started requiring programmers to submit weekly forms reporting on the number of lines of code they had written. Bill Atkinson thought that was silly. For the week in which he had rewritten QuickDraw’s region calculation routines to be six times faster and 2000 lines shorter, he put "-2000" on the form. After a few more weeks the managers stopped asking him to fill out the form, and he gladly complied."
{% endhighlight %}

Sometimes I think I'm just slow, because I didn't understand what this was trying to say at the beginning. I ask myself:

"Why his managers stops to bother him after he wrote -2000? I know, he improve the system trowing away the fat and... oh, I see!"

Yes, that was the right answer I was looking for. But, what does it mean? Well, as I understood the re-factory concept fits perfectly to negative code, and let me explain why.

There are many ways to solve a problem. Sometimes, we found many new problems to solve in our way and we keep coding and coding until we solute them. A few times our code is optimal, usually not. Other several times we found old and reviewed problems we already solved before, and we force the same solution to fit those new questions. As time passes this code lines get obsolete. The old code we used to use it's not worthing anymore. What should we do? Well, there are two solutions.

* We keep updating our code to fits new requirements, we make it more robust, more stable, more compatible with old code.
* Or we just simply trow away that old code and we remake it. Writing lean code, "same result less code".

All those lines we save using the second option, that is negative code.

We usually fell in a hole when someone says "do not re-invent the wheel, re-use". That, in a way, is correct. Why you should create a new tool if there are many out there? The answer is simple, because, sometimes, it's more than you need at that moment. Simple.

Remember, the lees code you use, the less code you maintain!

I encourage you to find better ways to solve the same problem. Trow away that awful lines you wanted to forget and improve them



