---
layout: post
title: Attack of the backtick
author: David Padilla
email: david@crowdint.com
avatar: d32b52ec6403614b1adf3e648cbbe584
---

It all started because I was getting some weird warning while running
the specs on one of my apps.

I know that, as a developer, I am suppposed to ignore the warning, but
this one felt ackward, so I had to track it down.

{% highlight bash %}
/Users/dab/.rvm/gems/ree-1.8.7-2010.02@project/gems/activerecord-3.0.3/lib/active_record/base.rb:1838: command not found:
{% endhighlight %}

What? Why is activerecord trying to run some command? what command? and
why it isn't finding that command?

Let's figure it out. So, I opened base.rb an went to line 1838 and all I saw was
something like this:

{% highlight ruby%}
def convert_number_column_value(value)
  if value == false
    0
  elsif value == true
    1
  elsif value.is_a?(String) && value.blank?
    nil
  else
    value
  end
end
{% endhighlight %}

Nothing useful there, line 1838 was just doing some if thing. So, what's
going on?

I went through lots of source files, trying to narrow down the
posibilites. But I just couldn't find anything unusual. If there was a
syntax error, why is the code not breaking completely, it's just a
warning on STDOUT.

I ended up doing the worst thing ever (for me, anyway) to figure out this
one, run the code with *ruby-debug*, put a couple of breakpoints. I feel
dirty just by admiting that.

Anyway, the problem was caused by some unintentional mistake:

{% highlight ruby %}
items_returned << item if item.cancelled?``
{% endhighlight %}

How could I have missed that? the *backticks* at the end of the line, of
course.

In ruby, the backtick method returns the standard output of running the string encompassed
by the backticks in a subshell.

Yes, it is a method, like almost every operator in Ruby.

A few examples:

{% highlight ruby %}
hey = `echo hey` # => "hey\n"
list_of_files = `ls` # => "file1\nfile2\nfile3\n"
{% endhighlight %}

It also sets a special variable $? with a Process::Status object with
the PID of that subshell.

{% highlight ruby %}
`exit 99`
$?           #  => # <Process::Status: pid 7781 exit 99>
$.exitstatus #  => 99
{% endhighlight %}

So, I guess what was happening is that, the *cancelled?* method was
taking the result of `` as a parameter, in this case, a string, printing the result of that to
STDOUT and just ignoring the value of the parameter.

In other words, I was calling *cancelled?* like this:

{% highlight ruby %}
cancelled?(``)
{% endhighlight %}

But, since cancelled wasn't expecting a parameter at all, it just
ignored the input. That's why I wasn't getting a syntax error or a
runtime error.

Probably nothing useful on my experience, since this is such a weird
problem to have, yet I just felt like explaining the power of the backticks.

Cheers
