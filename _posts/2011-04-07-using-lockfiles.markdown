---
layout: post
title: Using lockfiles in your Ruby based projects
author: Francisco Guzman
email: francisco.guzman@crowdint.com
avatar: c000ffd0c4ed3e23d09cae624d24b525
---

## Good code risk free

How many times have you found yourself in situations where you have to prevent some code from executing? Maybe you have a background task that runs automatically but also can be fired up manually; or chances are that someone tries to run a migration when a deployment script is halfway done. Rings a bell?

You might try to create your in-code solution or guidelines to prevent this from happening, but also, you can use one simple yet safe solution to make your life easier (which is something us Rails and Ruby in general developers love)

Your best friend for a situation like this is a *lockfile*...

### Lockfile to the rescue!!!

Lockfiles are temporary files that are created to work as a flag to indicate that a file is open or a process is running, that simple. So if you're going to run a critical task you really really want to use one of these.

There are a few gems out there that will handle this for you that also require quite a few lines of code to integrate with your code almost seamlessly; the one that I use in my current project is [cleverua-lockfile](http://github.com/cleverua/lockfile).

## Installation

### Rails 2.x

Add the following line to your _config/environment.rb_ file

{% highlight ruby %}
gem config.gem 'cleverua-lockfile'
{% endhighlight %}

Run *rake gems:install* from the command line

### Rails 3.x

Add this line to your _Gemfile_

{% highlight ruby %}
gem 'cleverua-lockfile'
{% endhighlight %}

And run *bundle install*

## Implementation

Now that you have the setup, it's time to add the functionality to your code; say your Rakefile... first, you have to:

{% highlight ruby %}
include Lockfile
{% endhighlight %}

and then, wrap your code with:

{% highlight ruby %}
with_lockfile(PATH_TO_YOUR_LOCKFILE) do
 your code ...
end
{% endhighlight %}

Example:

{% highlight ruby %}
include Lockfile

namespace :deploy do
 with_lockfile(PATH_TO_YOUR_LOCKFILE) do
   desc "Restarting mod_rails with restart.txt"
   task :restart, :roles => :app, :except => { :no_release => true } do
     run "touch #{current_path}/tmp/restart.txt"
   end

   [:start, :stop].each do |t|
     desc "#{t} task is a no-op with mod_rails"
     task t, :roles => :app do ; end
   end

   desc "invoke the db migration"
   task:migrate, :roles => :app do
     send(run_method, "cd #{current_path} && rake db:migrate RAILS_ENV=#{stage} ")
   end
 end
end
{% endhighlight %}

This will create a lock file on this before it yields to your code and will not allow to yield the block again until the first one finishes (or the lockfile no longer exists). Obviously, when code finishes, it will remove the lockfile. That easy.

## Limitations

This will only work in \*nix based systems (but of course that is not a problem for you, is it?).

## Other choices

[lisa's ruby-lockfile](http://github.com/lisa/ruby-lockfile)

[ahoward's lockfile](http://github.com/ahoward/lockfile)

[joshnesbitt's lockfile](http://github.com/joshnesbitt/lockfile)

[seamusabshere's lock_method](http://github.com/seamusabshere/lock_method)

[jcoby's lockfile](http://github.com/jcoby/lockfile)

## Enjoy!!!

