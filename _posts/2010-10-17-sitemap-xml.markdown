---
layout: post
title: Improve your SEO - sitemap generator
author: Roberto Sanchez
email: roberto.sanchez@crowdint.com
avatar: 0a34cf6219c554ee75506e544473aea1
---

Having a sitemap is the way search engines like Google, Yahoo, Bing and others can crawl the information of all of the urls in our website.

There are tools for generating sitemaps, we could use one of those tools if our website is static and it doesn't change that often. But, if we have a website that is continuously changing and generating new urls, we would need a way to dynamically generate our sitemap in a regular basis.

Let's use Rails to dynamically generate a sitemap on request.

In our example we will assume we have a small e-commerce website where we have categories for the products. So, the first thing we do is to define a controller action:

{% highlight ruby %}
def sitemap
	@categories = Category.find(:all,
		:select => 'path, updated_at', 
		:conditions => ['active = ?', true], 
		:order=> 'updated_at DESC')
	render :layout => false, :template => "layouts/sitemap"
end
{% endhighlight %}

With this method defined, we retrieve all the categories we want to have on the sitemap. And the next step is to create the xml format to display all the urls, we will create a template called sitemap.rxml

{% highlight ruby %}
xml.instruct!
xml.urlset(:xmlns=> 'http://www.sitemaps.org/schemas/sitemap/0.9') do
	xml.url do
		xml.loc("http://www.mywebsite.com")
		xml.lastmod(Time.now.xmlschema)
		xml.changefreq("weekly")
		xml.priority("0.5")
	end
	
	categories.each do |category|
		xml.url do
  			xml.loc(URI::decode url_for(:controller => "categories", :action => "sitemap", :id => category.path, :only_path => false))
  			xml.lastmod(category.updated_at.xmlschema)
  			xml.changefreq("weekly")
  			xml.priority("0.5")
		end
	end	
end
{% endhighlight %}

Pretty simple, don't you think? We just have to iterate through our collection to generate all the links from our categories. The parameter *:only_path => false* is necessary to produce the absolute url of our website as sitemaps require to have a protocol and a domain.

The parameters of the xml are straightforward:

* *loc* is the url
* *lastmod* is the last modified date of that url
* *changefreq* is how often this url is updated
* *priority* is the importance of this url compared to the rest of the urls in our website

As a final step, we add the route to the sitemap in *config/routes.rb*

{% highlight ruby %}
map.connect "sitemap.xml", :controller => "categories", :action => "sitemap"
{% endhighlight %}

And that's it, if we go to our www.website.com/sitemap.xml we will be able to see our generated sitemap. 

If we want our sitemap to be generated periodically, we can create a rake task to call this method and use a cron job to schedule it for example, once a week.

Give it a try and you will have a nice and easy way to generate the sitemap.xml for your webiste!
