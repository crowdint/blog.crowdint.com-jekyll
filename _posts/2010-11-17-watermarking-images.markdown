---
layout: post
title: Watermarking images minimizing the overhead
author: Francisco Guzm&aacute;n
email: francisco.guzman@crowdint.com
avatar: c000ffd0c4ed3e23d09cae624d24b525
published: false
---

During the days I was working for a project called [Creative Allies](http://creativeallies.com), I faced the need to find a way to stamp watermarks in user images; some of them were files with long width/height attributes and though the process was apparently simple, in practice our servers even crashed the app due to overhead.

After doing some research, I decided to come up with my own solution and now I want to share it here, hoping somebody will find it helpful. Let's get started.

##What I did the first time

The feature included uploading a file, validating it using [RMagick](http://rmagick.rubyforge.org/) and if validation passed, creating thumbnails and a watermarked preview.

For this post's purposes I will only re-create such functionality specifying a fixed path to file so I we can compare benchmark outcome. Ready?

###Opening the file

We'll assume our file was uploaded from a form and it's already in our app's temp folder, under the name ["wallpaper_original.jpg"](/images/wallpaper_original.jpg) (the file is 1.1 Mb)
{% highlight ruby %}
example_image_path = "#{RAILS_ROOT}/public/images/wallpaper_original.jpg"

 # Let's also specify the file path for our watermarked image
example_image_output_path = "#{RAILS_ROOT}/public/images/wallpaper_watermarked.jpg"
{% endhighlight %}
Image dimensions are: 1800 x 1200 pixels (big enough)

Also, we have the [watermark stamp](/images/watermark.png) in our images folder.
Watermark dimensions are: 250 x 250 pixels

###Stamping the watermark
I used a method called _apply\_watermark_ that will receive the path to our image as a string and the path of the watermarked image, like this:

{% highlight ruby %}
 def apply_watermark(image_path, new_filename_path)
  dst = Magick::Image.read(image_path).first
  src = Magick::Image.read("#{RAILS_ROOT}/public/images/watermark.png").first
  GC.start # great idea to fire up the garbage collector here, it's a good practice
  new_filename = new_filename_path
  rows = (dst.rows.to_f/src.rows).ceil
  cols = (dst.columns.to_f/src.columns).ceil
  wcolumns = src.columns
  wrows = src.rows
  lightness =0.075
  rows.times do |row|
    cols.times do |col|
      dst = dst.watermark(src, lightness, 1.0, col * wcolumns, row * wrows)
    end
  end
  dst.write(new_filename)
  dst.destroy!
  src.destroy!
end
{% endhighlight %}

Now we just have to call our _apply\_watermark_ method passing the _example\_image\_path_ string as the _image\_path_ and the _new\_filename\_path_ arguments:

{% highlight ruby %}
apply_watermark(example_image_path, example_image_output_path)
{% endhighlight %}

Now let's add some code to our _apply\_watermark_ method to perform [benchmarking](http://ruby-doc.org/core/classes/Benchmark.html):

{% highlight ruby %}
 def apply_watermark(image_path, new_filename_path)
  require 'benchmark'
  dst = Magick::Image.read(image_path).first
  src = Magick::Image.read("#{RAILS_ROOT}/public/images/watermark.png").first
  GC.start
  new_filename = new_filename_path
  rows = (dst.rows.to_f/src.rows).ceil
  cols = (dst.columns.to_f/src.columns).ceil
  wcolumns = src.columns
  wrows = src.rows
  lightness =0.075
  rows.times do |row|
    cols.times do |col|
      Benchmark .bm do |x|
        x.report {
          dst = dst.watermark(src, lightness, 1.0, col * wcolumns, row * wrows)
        }
      end
    end
  end
  dst.write(new_filename)
  dst.destroy!
  src.destroy!
end
{% endhighlight %}

I entered the console, picked our image's file path, called the method, grabbed a cup of coffee and relaxed... you can see the console output [here](https://gist.github.com/661883)

###Here's what happened

The code in our _apply\_watermark_ method iterated as many times our watermark file could fit into our original wallpaper image, stamped the watermark once and again until iterations were completed. This demanded memory and processing power and took longer than any user out there would expect. Sometimes our app just hung.

You can see the result here: [wallpaper_watermarked.jpg](/images/wallpaper_watermarked.jpg)

##How I fixed it

Sometimes users would upload HUGE files that were about 8000 x 5000 pixels and everything would just fall apart. We needed to find a way to reduce the number of iterations regardless the dimensions of the images; so I created an additional method that would pick a watermark stamp depending on how big the new image was and then using it in the iterations. Results just rocked!

{% highlight ruby %}
def pick_watermark(file)
  image_width = file.columns / 250
  case image_width.to_i
  when 0.. 2
    watermark = "#{RAILS_ROOT}/public/images/watermark.png"
  when 3..5
    watermark = "#{RAILS_ROOT}/public/images/watermark3.png"
  when 6..11
    watermark = "#{RAILS_ROOT}/public/images/watermark6.png"
  when 12..17
    watermark = "#{RAILS_ROOT}/public/images/watermark12.png"
  when 18..23
    watermark = "#{RAILS_ROOT}/public/images/watermark18.png"
  else
    watermark = "#{RAILS_ROOT}/public/images/watermark24.png"
  end
end

def apply_watermark(image_path, new_filename_path)
  require 'benchmark'
  dst = Magick::Image.read(image_path).first
  src = Magick::Image.read(pick_watermark(dst)).first
  GC.start
  new_filename = new_filename_path
  rows = (dst.rows.to_f/src.rows).ceil
  cols = (dst.columns.to_f/src.columns).ceil
  wcolumns = src.columns
  wrows = src.rows
  lightness =0.075
  rows.times do |row|
    cols.times do |col|
      Benchmark .bm do |x|
        x.report {
          dst = dst.watermark(src, lightness, 1.0, col * wcolumns, row * wrows)
        }
      end
    end
  end
  dst.write(new_filename)
  dst.destroy!
  src.destroy!
end

{% endhighlight %}

Here's the output from our tweaked code (I couldn't even grab another cup of coffee):

<script src="https://gist.github.com/661917.js?file=gistfile1.rb"></script>

Pretty amazing, uh?

We saved memory and processor power by 'pre-processing' the watermark stamps and putting them together in separate files so we could pick the one that would demand the lowest number of iterations in our code. After this tweak, our app was ready for the next move...

##Enjoy!