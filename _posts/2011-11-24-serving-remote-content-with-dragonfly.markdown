---
layout: post
title: Serving Remote Content with Dragonfly
author: Nora Alvarado
email: nora.alvarado@crowdint.com
avatar: 93d9cbaef8e5ceb60881c162e0c4597a
published: true
---
In the last project I've been working on, here at __CrowdInteractive__, an issue came up when serving images on the site. We have our app on _Heroku_ and also we have a shop on _Shopify_ as an external service; one of the features we had to implement was to upload, process and serve images, for that we use the Dragonfly gem, it stores original versions of content in a datastore which could be the filesystem, s3, or any other storage service. The thing is, we needed to upload the images in the Heroku app but serve them on the Shopify side.

When we upload images with [Dragonfly](https://github.com/markevans/dragonfly) in a datastore like Amazon S3 and request the URL of the dragonfly image, dragonfly serves an URL  relative to the site where we upload the image, therefore, when we render the image in the browser it receives a permanent redirect (301) to the original URL of the image at Amazon S3, and if we also want to resize the image, dragonfly applies the resize on that moment.

Both situations lead to performance issues, the need to redirect and resolve the original image URL, and at the same moment use the server time to apply the resize process to the image. So if we have a web site, where we render a lot of images, a significant delay may be observed.

An easy way to solve both problems, is using a couple of methods which Dragonfly provides to help us to remotely serve processed versions of content such as thumbnails. 

For this case, we just needed to create a _Thumb_ table to store the jobs with two strings columns:  _job_ and _uid_, and then just add the configuration block for the __before\_serve__ and __define\_url__ methods inside the _app.configure do |config| block_ in on our __../initializers/dragonfly.rb__ as follows:

{% highlight ruby %}
require 'dragonfly'

app = Dragonfly[:app_name]
app.configure_with(:imagemagick)
app.configure_with(:rails)

# Is up to us to determine or not an expiration time for the thumbnails
app.cache_duration = 3600*24*365*3 

app.configure do |config|
  config.url_host = Rails.env.production? ? 'http://myapp.herokuapp.com' : 'http://localhost:3000'
    # First we configure our before_serve method,
    # Before serving, the first time it is requested stores the thumbnail in the datastore
  config.server.before_serve do |job, env|
    uid = job.store
    # Keep track of its uid
    # Holds all the job info, e.g fetch 'image_uid' then resize to '40x40'
    Thumb.create!( :uid => uid, :job => job.serialize )    
  end

  # Next we define the url for our processed images, overriding the default .url method...
  config.define_url do |app, job, opts|
    thumb = Thumb.find_by_job(job.serialize) 
    # If (the job fetch 'image_uid' then resize to '40x40') has been stored already..
    # then serve the url from the datastore filesystem, s3, etc
    if thumb
      app.datastore.url_for(thumb.uid) 
    else
    # ...otherwise if the job hasn't been stored, serve it from the Dragonfly server as usual
      app.server.url_for(job)  
    end
  end
end
...
{% endhighlight %}

This would give us the first time the job is stored:

__image.thumb('40x40').url__  _normal dragonfly url e.g. /media/image..._

Then from the second time onwards:

__image.thumb('40x40').url__     _datastore url  /my-bucket.s3.amazonaws.com/2011…_

This solution allowed us to cache the images, avoid permanent redirects, decrease dragonfly's jobs load, and an overall improvement on our site performance. So, hope you could find this post useful and see you next time! XD

### -Nora

_PS. Thanks to Mario 'Chido' and Mumo to help me out with some concepts!!_
