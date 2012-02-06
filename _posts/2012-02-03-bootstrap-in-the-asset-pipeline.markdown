---
layout: post
title: Twitter's Bootstrap in the Asset Pipeline
author: David Padilla
email: david@crowdint.com
avatar: d32b52ec6403614b1adf3e648cbbe584
---

Hello. We're in February already. We've been so unbelievable busy at
[Crowd Interactive](http://www.crowdint.com) that we almost forgot about
our blog, but worry not, we're back.

So, you've probably heard about
[Twitter's Bootstrap](http://twitter.github.com/bootstrap/), the new hotness
when it comes to CSS frameworks.

I've been using it for a couple of websites where design doesn't really matter
and it has really helped me make them look good without too much effort. I even
[wrote a gem](https://github.com/dabit/bootstrap-form) to make forms look good
even faster, though, since [SimpleForm](https://github.com/plataformatec/simple_form)
just got Bootstrap too, I think it became a way better and more complete option
to style your forms.

Well, the only piece that was missing for me was being able to use Bootstrap in
the [Rails 3.1+ Asset Pipeline](http://guides.rubyonrails.org/asset_pipeline.html),
so I could write my own CSS classes along with the Bootstrap mixins.

For this example, let's pretend that we want to use Bootstrap to make all the
tables on our website look good. If you take a look at the
[documentation](http://twitter.github.com/bootstrap/base-css.html#tables), when
you are using the Bootstrap CSS file directly, to make a table look good you
would have to add a couple of classes, depending on what you want to see:

{% highlight html %}
<table class="table table-bordered table-striped">
  ...
</table>
{% endhighlight %}

There's two problems here, first, look at all those classes!, second, think
of all the tables that will be part of your website. What will happen when you
want to remove the border on all of them? You'd have to edit one by one and
remove the _.table-bordered. class_ A lot of unnecessary work, if you ask me.

This is a case where you want to use the asset pipeline to mix Bootstrap with
your own classes.

First thing you have to do is include the excellent
[less-rails-bootstrap](https://github.com/metaskills/less-rails-bootstrap) gem
in your Gemfile.

{% highlight ruby %}
  gem 'less-rails-bootstrap'
{% endhighlight %}

Now, edit your _app/assets/stylesheets/application.css_ file and require bootstrap:

{% highlight css %}

/*
 *= require 'twitter/bootstrap'
 *= require_self
 *= require_tree .
*/

{% endhighlight %}

This includes all the Bootstrap CSS directly into your own CSS, the same thing
as including it as a separate file on your HTML headers. Look at your website,
it already looks better just by doing this.

Now, go ahead and create a file called _app/assets/stylesheets/tables.css.less_.

Before you can use Bootstrap mixins on a _less_ file, you always have to import
these two mixin files at the very top:

{% highlight css %}
@import "twitter/bootstrap/variables";
@import "twitter/bootstrap/mixins";
{% endhighlight %}

Don't worry, if you don't use them at all, no code will be added to the resulting
CSS file.

For our specific example, we want to use the tables mixins, so we import
that file too:

{% highlight css %}
@import "twitter/bootstrap/variables";
@import "twitter/bootstrap/mixins";
@import "twitter/bootstrap/tables";
{% endhighlight %}

Now, we can use the table mixins on our own classes or elements. In this case,
I want _all_ the tables to look exactly the same:

{% highlight css %}
@import "twitter/bootstrap/variables";
@import "twitter/bootstrap/mixins";
@import "twitter/bootstrap/tables";

table {
  .table;
  .table-bordered;
  .table-striped;
}
{% endhighlight %}

This makes all the tables on the website look exactly the same, all bordered and
striped.

Your HTML suffers no more from class-itis:


{% highlight html %}
<table>
  ...
</table>
{% endhighlight %}

And still, all tables, look bordered and striped!

If, for example, you just want to get rid of the table border, you'd just remove the
_.table-bordered_ mixin from this _.less_ file, not from all the view files.
Cake.

You might want to take a look at all the _bootstrap .less_
[source files](https://github.com/twitter/bootstrap/tree/master/less) to get
a better idea of all the mixins that you can use, they all have great inline
documentation.

## Update 02/06

Thanks to [hishma](https://gist.github.com/hishma) for this excellent tip.

You can achieve the same thing with the [bootstrap-sass gem](https://github.com/thomas-mcdonald/bootstrap-sass)
as [explained on this gist](https://gist.github.com/1749504).

This way, if you are uncomfortable using less for your css files you can still
use scss.

## More reading

* [http://guides.rubyonrails.org/asset_pipeline.html](http://guides.rubyonrails.org/asset_pipeline.html)
* [http://www.lesscss.org](http://www.lesscss.org)
* [http://twitter.github.com/bootstrap/](http://twitter.github.com/bootstrap/)



