---
layout: post
title: Has Cucumber become more ... "Steak"?
author: Mario Chavez
email: mac@crowdint.com
avatar: 2ae35d2e0dfd4ca72c07ead1eec4a8f7
published: true
---
Today in Twitter I found a tweet about a big change in [Cucumber](http://cukes.info), the BDD Framework for Rails - well not only for Rails at this point -. Looking at the commit that introduces this change, there was a sentence that summarize very well what the change is about:

> "Cucumber scenarios should not be a series of steps that describe what a user clicks. Instead, they should express what a user *does*"

What this means, is that since the first version of Cucumber we have learned that the way to write Cucumber scenarios is:

{% highlight ruby %}
    Feature Signin
    In order to use the website
    As a user
    I want to signin

    Scenario: Signin with valid credentials
        Given I am on signin page
        When I fill in 'Email' with 'mail@example.org'
        And I fill in 'Password' with 's3cr3t'
        And I press 'Login'
        Then I should see 'Welcome'
{% endhighlight %}

And the scenario steps are:

{% highlight ruby %}
    Given /^(?:|I )am on (.+)$/ do |page_name|
      visit path_to(page_name)
    end

    When /^(?:|I )fill in "([^"]*)" with "([^"]*)"$/ do |field, value|
      fill_in(field, :with => value)
    end

    When /^(?:|I )press "([^"]*)"$/ do |button|
      click_button(button)
    end

    Then /^(?:|I )should see \/([^\/]*)\/$/ do |regexp|
      regexp = Regexp.new(regexp)

      if page.respond_to? :should
        page.should have_xpath('//*', :text => regexp)
      else
        assert page.has_xpath?('//*', :text => regexp)
      end
    end
{% endhighlight %}

We have seen scenarios like this in so many places: blog posts, [screencasts](http://railscasts.com/episodes/155-beginning-with-cucumber) and [books](http://pragprog.com/book/achbd/the-rspec-book).

But what is wrong with this scenario? well, I would say that nothing is wrong - per se -, it does what it is meant to do; it does add some additional value to our test since it describes in a very simple language - not programmer language - the steps and the expectation of our software behavior. And this is great if we are working together with non-technical people, but if the only people involved in writing and reading those scenarios are technical people, it becomes cumbersome to write and to maintain.

The steps for our scenario were created when we installed Cucumber in our project, they exists in features/step_definitions/web_steps.rb. In this example we did not have to write anything more than the text that describes our scenario, but web_steps aren't enough for most of the cases in our scenarios so we have to write our own.

Probably this why when [Steak](https://github.com/cavalle/steak) become public it had some attraction and opened the discussion about the Cucumber-way. Steak is a DSL on top of Rspec-Rails that allow us to write features and scenarios - like Cucumber - but without the cumbersome of being too verbose for us the programmers; this reduce the maintenance burden, when for some reason, we have to change the scenarios.

{% highlight ruby %}
    feature "Sigin" do
        scenario 'Signin with valid credentials' do
            visit '/signin'
            fill_in('Email', :with => 'mail@example.org')
            fill_in('Password', :with => 's3cr3t')
            click_button('Login')

            page.should have_content('Welcome')
        end
    end
{% endhighlight %}

Later on Steak was merge with [Capybara](https://github.com/jnicklas/capybara) but kept the same philosophy. Personally I did favor Steak over Cucumber and I have used for several [projects](http://blog.crowdint.com/2011/07/06/acceptance-testing-with-steak.html).

A few weeks ago when someone here at Crowd had to review a project that was tested with Steak, told me that he had a hard time trying to figure out what some of the
scenarios were doing just by looking at the specs, he told me that if the scenarios were written in Cucumber it could have been more easy for him to understand what was going on.

Somehow this person had a point, it definitely takes more time to learn what a scenario is doing in Steak than in Cucumber. This leads us back to the beginning of this post; are we misusing Cucumber? why we can't have the best of both worlds "Cucumber-Steak", well starting from version 1.1.0 we could have this; actually we don't have to wait for that version, we can do it today, but after this commit Cucumber itself will force us to change the way that we work with it.

First this new version will not provide the features/step_definitions/web_steps.rb file, so we are alone, we would not be able to use steps like "When I fill in 'Email' with 'mail@example.org'" out of the box. And we have to write our scenarios to express what the user does:

{% highlight ruby %}
    Feature Signin
    In order to use the website
    As a user
    I want to signin

    Scenario: Signin with valid credentials
        Given I have signed up with 'mail@example.org'
        Then I should see 'Welcome'
{% endhighlight %}

Our scenario steps become:

{% highlight ruby %}
    Given /^I have signed up as "([^"]*)"$/ do |email|
        visit(signup_path)
        fill_in('Email', :with => email)
        fill_in('Email', :with => email)
        fill_in('Password', :with => 's3cr3t')
        fill_in('Password Confirmation', :with => 's3cr3t')
        click_button('Sign up')
    end

    Then /^I should see "([^"]*)"$/ do |message|
        page.should have_content(message)
    end
{% endhighlight %}

In my opinion this simple change brings the best of both worlds, our scenario is more clean, easy to read and easy to maintain, the most important is that this is not a change in the Cucumber code, but a change in the mind set on how to use Cucumber.

If you want to take a look by your self at the commit that implemented this change here is the [link](https://github.com/cucumber/cucumber-rails/commit/f027440965b96b780e84e50dd47203a2838e8d7d)
