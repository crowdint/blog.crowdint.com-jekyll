---
layout: post
title: How to implement an email Interceptor for development
author: Mario Chavez
email: mac@crowdint.com
avatar: 2ae35d2e0dfd4ca72c07ead1eec4a8f7
published: false
---
Today is common to have an application that send automatic emails, they can be to recover user credentials or to notify users or web application management of
a exceptional event.

When we are in the development process often we need to use or demo this functionality, the only problem to this is to actually have to send an email to a real email address, if we use a fake email address no email is sent and a error is logged, if we go with the a real email address we could end up with a lot of test emails being send to coworkes or friends.

Fortunatelly Rails have a facility to intercept emails sent from our application and route them to a test or development email address.

Our email interceptor must implement one class method self.delivering_email(message), where message will represent the email that we want to send. Inside this method we can chanche for example the subject and mail to address for our own test email address.

{% highlight ruby %}
class DevelopmentMailInterceptor
  def self.delivering_email(message)
    message.subject = "#{message.to} #{message.subject}"
    message.to = "my_test_email@crowdint.com"
  end
end
{% endhighlight %}

Now that we have our interceptor ready, we need to tell Rails to use it.

As you have already noticed my interceptor name is DevelopmentMailInterceptor, this means - at leats for me - that we wnat to use it only in development mode, so lets set it up.

Usually in a Rails application we have a smtp initializer inside config/initializers - maybe a smtp_setup.rb, something like:

{% highlight ruby %}
ActionMailer::Base.smtp_settings = {
  :address  => ENV['MAIL_SMTP'],
  :port     => ENV['MAIL_PORT'].to_i,
  :domain   => ENV['MAIL_DOMAIN'],
  :user_name => ENV['MAIL_USER'],
  :password   => ENV['MAIL_PASSWORD'],
  :autentication => 'plain',
  :enable_starttls_auto => true
}
{% endhighlight %}

After out initialization, lets register our
interceptor with:

{% highlight ruby %}
ActionMailer::Base.register_interceptor(DevelopmentMailInterceptor) if Rails.env.development?
{% endhighlight %}

Here we specify our interceptor class name - our development_mail_interceptor.rb live in lib/ directory - and we restrict it to being setup only for development.

We need to restart our application, after that we can go ahead and send an email from our application, and this time no matter which email address we use, all emails will be "intercepted" and sent to the email address that we specified in our interceptor. So no more filling up our friends or co-workes with "1, 2, 3, test" emails.
