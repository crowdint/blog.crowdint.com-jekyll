---
layout: post
title: Multi-table models
author: Francisco Guzman
email: francisco.guzman@crowdint.com
avatar: c000ffd0c4ed3e23d09cae624d24b525
---

## ActiveRecord classes modeled after more than one table

This is an uncommon case, that's for sure. But at any moment, you could find yourself needing to have a model whose columns belong to different tables. This doesn't sound very Rails-ey, but this is ActiveRecord we're talking about, and I hope this helps you if you came here looking for this solution.

### default_scope

The definition in [ActiveRecord::Base documentation](http://api.rubyonrails.org/classes/ActiveRecord/Base.html#method-c-default_scope) shows that all operations on a model will use the defined scope. This is the key for modeling our AR class after more than one table.

## Let's see an example...

First off, let's pretend we absolutely need to do this. With that said, let's assume we have two tables: *persons* and *addresses* and we NEED to have an ActiveRecord class (model) called *customer*

Our ruby class will start by looking like this:

{% highlight ruby %}
class Customer < ActiveRecord::Base
  set_table_name :persons
end
{% endhighlight %}

Next, we *SELECT* the fields we want to use in our model, a _here document_ comes in handy:

{% highlight ruby %}
class Customer < ActiveRecord::Base
  set_table_name :persons

  SELECT_STATEMENT = <<-SELECT
    person.id AS id,
    person.name as name
    person.email as email
    addresses.street as street
    addresses.city as city
    addresses.state as state
  SELECT

end
{% endhighlight %}

The other component in our class modeling is the *JOIN* part of our statement:

{% highlight ruby %}
class Customer < ActiveRecord::Base
  set_table_name :persons

  SELECT_STATEMENT = <<-SELECT
    person.id AS id,
    person.name as name
    person.email as email
    addresses.street as street
    addresses.city as city
    addresses.state as state
  SELECT

  DEFAULT_JOIN = <<-JOIN
    LEFT JOIN addresses ON
    addresses.person_id = persons.id
  JOIN

end
{% endhighlight %}

Last, we put it all together as follows:

{% highlight ruby %}
class Customer < ActiveRecord::Base
  set_table_name :persons

  SELECT_STATEMENT = <<-SELECT
    person.id AS id,
    person.name as name
    person.email as email
    addresses.street as street
    addresses.city as city
    addresses.state as state
  SELECT

  DEFAULT_JOIN = <<-JOIN
    LEFT JOIN addresses ON
    addresses.person_id = persons.id
  JOIN

  default_scope select(SELECT_STATEMENT).joins(DEFAULT_JOIN)

end
{% endhighlight %}

And _voilà!!!_, our ActiveRecord class will behave as if its columns were all in the same table

{% highlight ruby %}

> Customer

=> Customer(id:integer, name:string, email:string, street:string, city:string, state:string)

{% endhighlight %}
### Constraints

Creating and deleting rows is not conventional. We have to write a bit of code to perform, for example, row deletion (you can not delet rows from a multitable query):

{% highlight ruby %}
Customer.send(:with_exclusive_scope) { Customer.delete_all }
{% endhighlight %}

_with_exclusive_scope_ will 'bypass' or discard the *default_scope* and will give us access to the columns in the *persons* table to perform the operations we otherwise couldn't perform because of the default_scope

## Enjoy!!!

