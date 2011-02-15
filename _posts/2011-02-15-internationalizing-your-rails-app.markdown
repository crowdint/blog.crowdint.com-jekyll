---
layout: post
title: Internationalizing your Rails application with i18n
author: Ana Rosas
email: ana.rosas@crowdint.com
avatar: 2f2bc78de007915554f49c3ea0fef125
---

In this now-globalized world, we frequently want to reach audience that does not speak our language or does not speak as fluently as we wished or the other way around we do not speak their language, so the more IT’s continue expanding in our society the more we need to communicate our business,  projects or simply our ideas in other countries.

So let’s say you have your company’s web page in a Rails application, and you want it to be available in three different languages: English (default), Spanish and French.

Well, there's good news! Setting these options is piece of cake with Rails! Since it includes the i18n API, (a.k.a Rails internationalization).
With this API you only need to split your contents into .yml or .rb files, which we will be calling dictionaries, one for each language you want in your page, for example: en.yml, es.yml, fr.yml
You have to save them in the config/locales folder in your app.

The ‘tricky’ thing is that you must follow a order in your files, they must have the exact same structure for the labels, so you just need to add the translation, the examples of the dictionaries will be done in .yml:


{% highlight ruby %}
en:
  main_page:
    hello: “Hello”
    welcome: “Welcome to   My Company”

{% endhighlight %}


{% highlight ruby %}
es:
  main_page:
    hello: “Hola”
    welcome: “Bienvenido a Mi Empresa”

{% endhighlight %}


{% highlight ruby %}
fr:
  main_page:
    hello: “Salut”
    welcome: “Bienvenue à Mon Entreprise”

{% endhighlight %}





To set up the app you just need to add the following into your _Application Controller_


{% highlight ruby %}
before_filter :set_locale

def set_locale
 I18n.locale = extract_locale_from_tld
end

def extract_locale_from_tld
 parsed_locale = request.host.split('.').last
 I18n.available_locales.include?(parsed_locale.to_sym) ? parsed_locale  : nil
end
{% endhighlight %}

And you will need to create a scope in your routes files in order for your routes to work:

{% highlight ruby %}
match '/:locale' => 'home#index'

scope "(:locale)", :locale => /en|es|fr/ do
 resources :contact
end
{% endhighlight %}


With the _exctract\_locale\_from\_tld_ method in the ApplicationController the locale will be passed to the URL in the params, but you will get a not-so-nice url, like this: www.yourpage.com/contact?locale=en, so if you prefer to have more readable url, like for example www.yourpage.com/en/contact, you should use instead these methods:

{% highlight ruby %}
 before_filter :set_locale

 def set_locale
   I18n.locale = params[:locale]
 end

 def default_url_options(options={})
   {:locale => I18n.locale}
 end
{% endhighlight %}

In order to switch language in your application you will probably need a menu with the supported languages, and your menu will need to pass the ##:locale of the selected language, to do it you can do something like this

{% highlight ruby %}
.menu
  = link_to("English", :locale => "en")
  = link_to("Française", :locale => "fr")
  = link_to("Español", :locale => "es")
{% endhighlight %}

Now you need to tell rails where it is going to look for the dictionaries, and which language will be the default, usually (if you haven’t removed them) you will have this lines commented in the application.rb file.

{% highlight ruby %}
  config.i18n.load_path += Dir[Rails.root.join('my', 'locales', '*.{rb,yml}').to_s]
  config.i18n.default_locale = :en
{% endhighlight %}

If you have a lot of sections in your page, and you would like to have your dictionaries in separate folders depending on their section, you can save the folders in config/locales,  and have something like this, config/locales/views/home and save in there all the dictionaries for the home page.

{% highlight ruby %}
en:
 views:
    home:
      hello: “Hello”
      welcome: “Welcome to   My Company”
{% endhighlight %}

{% highlight ruby %}
es:
 views:
    home:
      hello: “Hola”
      welcome: “Bienvenido a Mi Empresa”
{% endhighlight %}

{% highlight ruby %}
fr:
 views:
    home:
      hello: “Salut”
      welcome: “Bienvenue à Mon Entreprise”
{% endhighlight %}


And of course you will need to change the path you set in the application.rb file to something like this

{% highlight ruby %}
  config.i18n.load_path += Dir[Rails.root.join('config', 'locales', '**', '*.{rb,yml}')]
{% endhighlight %}

And finally, how do we get the text we want from our dictionaries files?
Well it’s easy, you just need to put the tags in the right order to obtain the text, for example, with:

{% highlight ruby %}
.content
  %h1
    = t("views.home.hello")
  %p
    = t("views.home.welcome")
{% endhighlight %}


Something cool about this, is that in your dictionaries you can use HTML to give your text some style or format


{% highlight ruby %}
en:
views:
  home:
    hello: “Hello”
    welcome: “Welcome to <b>Crowd Interactive</b>”
{% endhighlight %}


If you do this, you will need to use ‘raw’ to escape the HTML...

{% highlight ruby %}
.content
  %h1
    = t("views.home.hello")
  %p
    = raw t("views.home.welcome")
{% endhighlight %}


Well, as you can see, it is pretty easy to set up i18n, and I hope this small guide help you through the internationalization process of your site!