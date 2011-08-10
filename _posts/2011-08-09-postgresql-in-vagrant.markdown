---
layout: post
title: How to install Postgresql 8.4 in a Vagrant box
author: Mario Zaizar
email: mario@crowdint.com
avatar: 29612de9aa10447dc2f9ec84cf2ab1b2
---

If you don't know what Vagrant is, go read our [first post about it](http://blog.crowdint.com/2011/06/21/vagrant.html)

## Introduction
Ok, we've been using `Vagrant` to manage our servers and services for a while, and we love it.
It's clean, easy and we don't mess with our original systems.
Last week we started a new proyect that uses `postgresql`, so we tried doing the common `apt-get install postgresql` 
but nothing seemed to work, there is a [chef](http://vagrantup.com/docs/provisioners/introduction.html) recipe,
but it looks like it has some issues and we didn't have enough time to do the research.
In one word: Frustration.

So, what did we do?, we installed it with our bare hands, step by step.

---

## Let's start!
Start your vagrant box.

{% highlight bash %}
you$ vagrant up
you$ vagrant ssh
{% endhighlight %}

## 1) Fixing the 'Please check that your locale settings' error
Seems like the current box from vagrant have issues with postgres & lang settings. Easy to fix..

{% highlight bash %}
vagrant$ sudo vim /etc/bash.bashrc

# Add this lines to the bottom:
export LANGUAGE=en_US.UTF-8
export LANG=en_US.UTF-8
export LC_ALL=en_US.UTF-8
{% endhighlight %}

Save & Exit. Then run these commands:

{% highlight bash %}
vagrant$ sudo locale-gen en_US.UTF-8
vagrant$ sudo dpkg-reconfigure locales
{% endhighlight %}

Ok, now your system has configured the default LANG. No more 'LANG=C' error. 

## 2) Install postgres 8.4, and the comunity extras

{% highlight bash %}
vagrant$ sudo apt-get install postgresql postgresql-contrib; 
{% endhighlight %}

Add postgres's binaries to the PATH.

{% highlight bash %}
vagrant$ sudo nano /etc/bash.bashrc

# Add this lines to the file:
export PATH=$PATH:/usr/lib/postgresql/8.4/bin/
export PGDATA=/usr/local/pgsql/data
{% endhighlight %}

Save & Exit. Ok, now you can run `postgres --version` and other postgresql executables.
Also, you've defined the default data folder, you will need that soon (1).

### Setup postgres's user, and data folder.

Postgresql server should be started by his own system user, 
and it have been created by the installation but using a random password.
Now you'll change that password.

Change postgres's password.

{% highlight bash %}
vagrant$ sudo su
root# passwd postgres
# Type a new password, like 'postgres'
{% endhighlight %}

Init postgres data structure, and create the storage folder

{% highlight bash %}
root# mkdir /usr/local/pgsql/data
root# chown postgres /usr/local/pgsql/data
root# su postgres
{% endhighlight %}

Init the data folder

{% highlight bash %}
postgres$ initdb -D /usr/local/pgsql/data
{% endhighlight %}

*Note (1): Make sure this is the same directory as your `PGDATA` variable.*

### 3) Enable remote connections

{% highlight bash %}
postgres$ vim /usr/local/pgsql/data/postgres.conf

# Change this `listen_address='localhost'` to
listen_address='*'
{% endhighlight %}

Save & exit.

### Enable access from local network

{% highlight bash %}
postgres$ nano /usr/local/pgsql/data/pg_hba.conf

# Vagrant uses 33.33.33.10 like addresses
host all all 33.33.33.0/24 trust
{% endhighlight %}

Save & exit.

### Start server

{% highlight bash %}
vagrant$ su postgres
postgres$ postgres
{% endhighlight %}

## Done.
Congrats!

---

## Extras

### Notes:

* You can stop the server pressing `ctrl + C`.
* You can start the server in background using `postgres$ postgres &`
* You must start postgresql using postgres user. 
* *You could improve this post saying how to start postgresql as a service, would you?*
 
### GUI admin?
Now you can install tools like `pgAdmin3` to manage your postgresql server and databases directly from your host.
It's multiplatform and free.

### References:
* [Official postgres documentation](http://www.postgresql.org/docs/8.4/static/creating-cluster.html)
* [About the locale error](http://bookmarks.honewatson.com/2009/05/30/perl-warning-please-check-that-your-locale-settings-ubuntu/)
* [Download pgAdmin 3](http://www.pgadmin.org/)
* <del>Mom</del>

## Disclamer & feedback
Please note that all of these steps have been `discovered` by us entirely with some help of google's special powers...
feel free to add comments if you know of a better way to do something, or if there is a mistake
that need to be remarked. Also, if you are using Ubuntu, you probably won't need all of these steps,
as Ubuntu provides the server with all the structure, and preconfigured files.

Also, we should write another post describing how to use a Chef recipe for this
avoiding all this s**t... And it shall be called, `The Easy Way`, coming soon.

## Stay tuned!
### -Mario
