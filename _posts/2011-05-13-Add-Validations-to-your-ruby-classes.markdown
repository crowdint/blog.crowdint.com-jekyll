---
layout: post
title: Add Validations to yours Ruby classes
author: Omar Vargas
email: omar@crowdint.com
avatar: 433031df4018635dfb177f4066c37a3b
---

Validations are one of the most useful things in ActiveRecord.
To use them you only need to inheritance your class from ActiveRecord::Base,
but, What about if you don't need to save the records in a database?
Does it make sense to use ActiveRecord and its overload only for validations?
If you're wondering, the answer is NO.

Fortunately in Rails 3 validations were moved from *Active Record* to
*Active Model* this give us the ability to use validations in our ruby
classes in an easier way.

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
    contact.valid? <== false
{% endhighlight %}

or

{% highlight ruby %}
    contact.errors <== {:name=>["can't be blank"]}
{% endhighlight %}

Also we can use all the [Rails 3 Validations](http://edgeguides.rubyonrails.org/3_0_release_notes.html#validations). 

Hope this may help you
