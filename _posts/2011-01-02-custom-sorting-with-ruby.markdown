---
layout: post
title: Custom Sorting with Ruby
author: David Padilla
email: david@crowdint.com
avatar: d32b52ec6403614b1adf3e648cbbe584
---

Sometimes, the sorting of your array elements depend on something more
complex than just alphabetical order or numerical order.

From my experience, take for example shoe sizes for little kids. You have
shoes in size 10.5 through 13.5, then, the count is restarted to 1
through 9.

So, in order, the size table would look like this:

<table border="1">
<tr><td align="center">10.5</td><td align="center">11</td><td align="center">11.5</td><td align="center">12</td><td align="center">12.5</td></tr>
<tr><td align="center">13</td><td align="center">13.5</td><td align="center">1</td><td align="center">1.5</td><td align="center">2</td></tr>
<tr><td align="center">2.5</td><td align="center">3</td><td align="center">3.5</td><td align="center">4</td><td align="center">4.5</td></tr>
<tr><td align="center">5</td><td align="center">5.5</td><td align="center">6</td><td align="center">6.5</td><td align="center">7</td></tr>
<tr><td align="center">7.5</td><td align="center">8</td><td align="center">8.5</td><td align="center">9</td></tr>
</table>

Well, imagine we have those values on a ruby array like so:

{% highlight ruby %}
[1, 1.5, 2, 2.5, 3, 3.5, 4, 4.5, 6, 6.5, 7, 7.5, 8, 8.5, 9, 10.5, 11,
11.5, 12, 12.5, 13, 13.5]
{% endhighlight %}

How would we sort it? Well the answer is kinda simple. Using ruby's sort
method, or at least a customized version of it.

For the sake of legibility, let's make a short version of the array.

Let's pretend for a moment that if we had this array
{% highlight ruby %}
  values = [1, 1.5, 2, 2.5, 3, 12, 12.5, 13, 13.5]
{% endhighlight %}

It's sorted version should look like:
{% highlight ruby %}
  values = [12, 12.5, 13, 13.5, 1, 1.5, 2, 2.5, 3]
{% endhighlight %}

## The sort method

In Ruby, all Enumerables (mostly array and hashes) have a sort method.
An example on how to use it would be:

{% highlight ruby %}
  values = [12, 12.5, 13, 13.5, 1, 1.5, 2, 2.5, 3]
  values.sort {|a,b| a <=> b}
{% endhighlight %}

This code would sort the array in numerical order. The first thime I saw
this code, one thing came to my mind. What does that *<=>* operator
does?

To understand that, let's first figure out how the *sort* method works.

The sort method takes the elements on the enumerable in pairs,
and expects the result of the block to be:

{% highlight ruby %}
   -1  when a < b

    0  when a == b

    1  when a > b
{% endhighlight %}

That means the *<=>* operator, compares both elements on its sides
and returns -1, 0, 1 depending on the values.

{% highlight ruby %}
1 <=> 2 # => -1
3 <=> 3 # => 0 
4 <=> 1 # => 1
{% endhighlight %}

Try that on irb, I dare you.

Anyway, it seems that, like all operators, in this case *<=>* is defined
on the Fixnum class, and there's some code that returns the comparison
value accordingly.

In our case, we will use a custom function to return -1, 0, 1 depending
on the value of a and b.

For this example, let's put it on a Comparison class:

{% highlight ruby %}
class Comparison
  def self.little_kid_size(a, b)
    ...
  end
end
{% endhighlight %}

So, the way we'll use this method will be:


{% highlight ruby %}
  values = [12, 12.5, 13, 13.5, 1, 1.5, 2, 2.5, 3]
  values.sort {|a,b| Comparison.little_kid_size(a, b)
{% endhighlight %}

To solve this problem, first thing we need to do is assign a weight to
the two sets of sizes: -1 to [12, 12.5, 13, 13.5] and 1 to [1, 1.5, 2,
2.5, 3].

{% highlight ruby %}
class Comparison
  def self.little_kid_size(a, b)
    ...
  end

  def self.little_kid_size_weight(a)
    (a >= 12) ? -1 : 1
  end
end
{% endhighlight %}

Now, we'll use that weight calculation to determine the sort order of
the elements.

{% highlight ruby %}
class Comparison
  def self.little_kid_size(a, b)
    #
    # If the weights are the same, compare them numerically
    #
    if little_kid_size_weight(a) == little_kid_size_weight(b)
      a <=> b

    #
    # If the weights are different, just return the weight of a
    #
    else
      little_kid_size_weight(a)
    end
  end

  def self.little_kid_size_weight(a)
    (a >= 12) ? -1 : 1
  end
end
{% endhighlight %}

And that's it, you can try it on irb as it is.

This solution is specific to this specific problem, I just wanted to make an
example on how to use the sort method for advanced stuff.

All you have to do is remember to make the result of the sort block
return -1, 0, 1 accordingly. How you do that is all up to you.

Hope this helps you somehow.
