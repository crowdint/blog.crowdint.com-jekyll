---
layout: post
title: Colorful rails logs
author: Mario Chavez
email: mac@crowdint.com
avatar: 2ae35d2e0dfd4ca72c07ead1eec4a8f7
published: false
---
If you have terminal that support colorsi, like OSX Term or iTerm, you can add colors to log messages in a Rails application.

Usually when we need to log infomation that is useful for us we use Rails
logger like:
{% highlight ruby %}
logger.debug "Here is were I'm"
{% endhighlight %}

You will see that log message is being displayed in a plain, boring
white/grayinsh color and it just get lost with the rest of the log messages.

We could add some colors and fun to our logs with a very simple escape sequences.

To log a message with blue color we just need to start with a escape sequence
that set our color, we also need a stop escape sequence to change colo back:
{% highlight ruby %}
logger.debug "\033[0;34mHere is were I'm\033[0;37m"
{% endhighlight %}

If we go and look into our Rails log, it will be more easy to spot out message
within the rest of the log messages, this is because it will be colored with a bright blue color.

Rails logger can accept many more attributes than just changing colors, for
example we can set the following font attributes:
00=none 01=bold 04=underscore 05=blink 07=reverse 08=concealed

Or change text colors as we discussed earlier:
30=black 31=red 32=green 33=yellow 34=blue 35=magenta 36=cyan 37=white

Or we can algo change background colors to:
40=black 41=red 42=green 43=yellow 44=blue 45=magenta 46=cyan 47=white

So the next step, will be to create a reusable method to log colorful messages, maybe something like:
{% highlight ruby %}
logger.blue_debug "Hey I should be blue"
{% endhighlight %}

Now you know that your Rails logs can be fun and colorful.
