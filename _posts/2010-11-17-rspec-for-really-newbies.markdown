---
layout: post
title: RSpec for really newbies
author: Ana Rosas
email: ana@crowdint.com
avatar: 2f2bc78de007915554f49c3ea0fef125
short_date: Nov 17
---


First off, this post is meant to be a brief introduction for testing Rails applications with RSpec, I'll only get into model and controller testing.

## What is RSpec?  
Well, it’s easy, RSpec is a framework for Ruby that allows us make scenarios for our applications and help us test them.
The main goal about testing your objects with RSpec, is that you test what your object is going to do, [Behaviour Driven Development](http://en.wikipedia.org/wiki/Behavior_driven_development). The syntax is very simple, and human readable, this way you can read it as if you were having a conversation with your client to determine what exactly they want the project to do. It’s very easy to use, as long as you know exactly what you want to test, and here is where I think most of us usually get stuck...
So, after this brief introduction, let’s get to the action!

##How do we start an RSpec test for our rails application?

Say you already have a _Rails3_ project. In your Gemfile add the next lines.

{% highlight bash %}
group :development, :test do
  gem "rspec-rails" , ">= 2.2.0"
end
{% endhighlight %}

Next run the _bundle install_ command in your console in order to install all your gems in the Gemfile. After the bundle is finished write in your console

{% highlight bash %}
$ rails generate rspec:install
{% endhighlight %}

With this generator we get the necessary files to run the specs and the directory in our app where we are going to save them.
To finish with this small setup you should run the next commands to make sure everything is running fine in your app!

{% highlight bash %}
$ rake db:migrate
$ rake db:test:prepare
$ rake spec
{% endhighlight %}

After this, every time you generate from the console a new model or controller, RSpec will automatically create the spec file for you... Now you are more than ready to start testing!

##Let’s begin with Model testing

 _RSpec is all about expectations_. What are you expecting an object to do? That’s what you must be thinking when you start a new test.
Normally in a Model, what do you do? You basically validate the fields on your tables, or create definitions in order to get new queries, among other things.
So that’s what you must test, for this you have the following magic words: <br />

_describe, context, before, it and should_
                        
_describe_, as the word says, it will help you describe what you want the method to do. <br />
_context_, will help you to describe the environment or the background where your tests will be running at. <br />
_before_, will help you define stuff you need to be done before you test a method, for example, if you need to simulate an account, a shipping_cart, before is your friend. <br />
_it_, is meant to be, also, a descriptor for the situation you are testing. <br />
_should and should_\__not_, with this magic word, you will be able to compare if you’ve received what you were expecting. _Remember it’s all about expectations_.<br />

In the following example, we are going to test the Account Model in an application, the first step is to describe the model. Next we define the context and create some accounts in the before segment.
Now, we check that the created accounts have valid attributes, for that we created an account without an e-mail address, which we test in the second example. Finally the second context is meant to search an account by its e-mail address.

{% highlight ruby %}
describe Account do
    context "when an account is created it has to have an e-mail address" do
      before(:each) do
        @account = Account.create! :name => "Test", :email => "test@test.com"
        @account2 = Account.create :name => "Test2"
      end 
      
      it "validates the account attributes" do
        @account.should be_valid
      end

      it "doesn't validates the account attributes" do
        @account2.should_not be_valid
      end
    end

    context "when an account is searched by its e-mail address" do 
      before(:each) do
        @account = Account.create! :name => "Test Name", :email => "test@test.com"
      end

      it "should find the account" do
        Account.find_by_email("test@test.com").should be_true
      end
    end
end
{% endhighlight %}

##...Now Controllers

It’s basically the same thing as with models, the difference is that you need to simulate when you’re sending data to a method, what normally a POST, GET, PUT or DELETE request from our browser would be. This is done, for example, by typing _get :show, :id => @account.id_ in our test file where we say which method we want to use (_:show_) and which parameter we want to send (_:id_).
In the next example we test the show method from our Accounts controller, let’s say we want the method to find an account by its id. We go the same way as before, we create two accounts in the before segment, then we describe the method we are testing and what it should do. In this case as our example says, it should find the account by its id. The _assigns[:account]_ is what the method returns to the call. In this case the id, name and e-mail. <br />
The last example tests the _:new_ method in the controller, again we _post_ the name and email of the account and then we look for the registered name, if it's found then it has been successfully created.

{% highlight ruby %}
describe AccountsController do

  describe "GET show" do
    before(:each) do
      @account = Account.create! :name => "Name", :email => 'test@test.com'
    end
    
    it "should find the account by its id" do
      get :show, :id => @account.id
      assigns[:account].name.should == "Name"
    end
  end
  
  describe "POST new" do 
    it "should register the new account" do
      post :new, :account => {:name => "Name 3", :email => "test4@test.com"}
      assigns[:account].name.should == "Name 3"
    end
  end
end
{% endhighlight %}

To finish, I want to talk a little bit about the views, which are not often tested, at least with RSpec, if you want to get into view testing, I think it will be better for you to use [Webrat](https://github.com/brynary/webrat), [Cucumber](http://cukes.info/) or [Selenium](http://seleniumhq.org/). I'll just leave this for another post.

##Hope this will help you start with the long road of BDD!

##Resources

[The RSpec Book](http://www.pragprog.com/titles/achbd/the-rspec-book)<br />
[RSpec's homepage](http://rspec.info/)<br />
[Model testing](http://rspec.info/rails/writing/models.html)<br />
[Controller testing](http://rspec.info/rails/writing/controllers.html)<br />
