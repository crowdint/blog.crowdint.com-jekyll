---
layout: post
title: Add Validations to your Ruby classes
author: Omar Vargas
email: omar@crowdint.com
avatar: 433031df4018635dfb177f4066c37a3b
---

Validations are one of the most useful things in ActiveRecord.
To use them all you have to do is make your class inherit from ActiveRecord::Base,
but, what if you don't want to save the records in a database?
Does it make sense to use ActiveRecord and its overload only for validations?
In case you're wondering, the answer is NO.

Fortunately, in Rails 3, validations were moved from *ActiveRecord* to
*ActiveModel*. This give us the ability to use validations in our ruby
classes very easily, even if you don't want to use ActiveRecord.

{% highlight ruby %}
    # contact.rb
    class Contact
      include ActiveModel::Validations
      
      attr_accessor :name
      
      validates :name, :presence => true
      
    end
{% endhighlight %}

By including the *ActiveModel::Validations* module in our class we are able
to use methods like the following

{% highlight ruby %}
    contact = Contact.new
    contact.valid? # => false
{% endhighlight %}

or

{% highlight ruby %}
    contact.errors # => {:name=>["can't be blank"]}
{% endhighlight %}

We can use all of
the [Rails 3 Validations](http://edgeguides.rubyonrails.org/3_0_release_notes.html#validations).

Hope this may help you
