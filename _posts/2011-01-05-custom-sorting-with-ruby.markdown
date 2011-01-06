---
layout: post
title: Custom Sorting with Ruby
author: David Padilla
email: david@crowdint.com
avatar: d32b52ec6403614b1adf3e648cbbe584
---

Sometimes, sorting the elements in an array depend on something more
complex than just alphabetical order or numerical order.

From my experience, take for example shoe sizes for little kids.
For some of the vedors I've worked with you have shoes in size 10.5
through 13.5, then, the count is restarted to 1 through 9.

So, in order, the size array would look like this:

{% highlight ruby %}
[10.5, 11, 11.5, 12, 12.5, 13, 13.51, 1.5, 2, 2.5, 3, 3.5, 4, 4.5, 6, 6.5, 7, 7.5, 8, 8.5, 9]
{% endhighlight %}

## The problem

Imagine we have those values on a sorted array:

{% highlight ruby %}
[1, 1.5, 2, 2.5, 3, 3.5, 4, 4.5, 6, 6.5, 7, 7.5, 8, 8.5, 9, 10.5, 11,
11.5, 12, 12.5, 13, 13.5]
{% endhighlight %}

How would we properly sort it? Well the answer is kinda simple. Extending ruby's sort
method.

For the sake of legibility, let's make a short version of the array.

Let's pretend for a moment that if we had this array
{% highlight ruby %}
  values = [1, 1.5, 2, 2.5, 3, 12, 12.5, 13, 13.5]
{% endhighlight %}

It's sorted version should look like:
{% highlight ruby %}
  values = [12, 12.5, 13, 13.5, 1, 1.5, 2, 2.5, 3]
{% endhighlight %}

In Ruby, all Enumerables (mostly array and hashes) have a sort method.
An example on how to use it would be:

{% highlight ruby %}
  values = [12, 12.5, 13, 13.5, 1, 1.5, 2, 2.5, 3]
  values.sort {|a,b| a <=> b}
{% endhighlight %}

This code would sort the array in numerical order. The first thime I saw
this code, one thing came to my mind: What is that *<=>* operator?

To understand that, let's first figure out how the *sort* method works.

## The sort method

The sort method takes the elements on the enumerable in pairs,
receives a block, and expects the result of the block to be:

{% highlight ruby %}
   -1  when a < b

    0  when a == b

    1  when a > b
{% endhighlight %}

Based on the result of that block, the sort method returns the
array in order.

## The <=> operator

That means the *<=>* operator, compares both elements on its sides
and returns *-1, 0, 1* depending on the values.

An example of how it works for the Fixnum class:

{% highlight ruby %}
1 <=> 2 # => -1
3 <=> 3 # => 0 
4 <=> 1 # => 1
{% endhighlight %}

Go on, try that on *irb*.

In our case, we will use a custom function to return *-1, 0, 1* depending
on the value of *a* and *b*.

For this example, let's just put it on a Comparison class:

{% highlight ruby %}
class Comparison
  def self.little_kid_size(a, b)
    ...
  end
end
{% endhighlight %}

So, the way we'll use this method will be:

{% highlight ruby %}
  values = [1, 1.5, 2, 2.5, 3, 12, 12.5, 13, 13.5]
  values.sort { |a,b| Comparison.little_kid_size(a, b) } #=> [12, 12.5, 13, 13.5, 1, 1.5, 2, 2.5, 3]
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

## Conclusion

All you have to remember is to make the result of the sort block
return -1, 0, 1 depending on how you want to sort the values.

If the sizes were stored, for example, on a ShoeSize model, the best
thing to do is override the ShoeSize class *<=>* operator. That would
actually look much better, but you can always implement the sort methods on a
Module, or an external class to reuse them.

Hope this helps you somehow.
