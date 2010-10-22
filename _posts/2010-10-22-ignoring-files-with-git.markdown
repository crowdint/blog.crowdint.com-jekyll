---
layout: post
title: Ignoring files with Git
author: Daniel Gaytán
email: daniel@crowdint.com
avatar: 554ca5879badb8dfd61f5ea7e54aef9e
published: true
---

When you work on an application there are some files you do not really need in the project (eg. database files, logs, dynamic created files, etc.)

Fortunately our best versioning system friend, Git, comes with the ability to ignore those files. You need to include a file in one of your project folders with the name ".gitignore"

{% highlight bash %}
touch .gitignore
{% endhighlight %}

On this file you will include those files you do not want to be versioned:

{% highlight bash %}
some_file.rb
*.log
doc/**/*
public/images/system/**/*
{% endhighlight %}

As you see, you could include the file name or include many ones with the known wildcards for listing files.

Git by default ignores empty folders, so if your application requires a folder to exist, you just need to create a blank .gitignore file.

But what if you must include a file inside a folder among others you want to ignore? such as config files, or whatever. Then you may want to prevent git from ignoring those files:

{% highlight bash %}
config/*
!config/a_special_file.rb
{% endhighlight %}

Now Git will ignore all the files but the special file you need to be versioned.

I hope this was helpul for someone.

