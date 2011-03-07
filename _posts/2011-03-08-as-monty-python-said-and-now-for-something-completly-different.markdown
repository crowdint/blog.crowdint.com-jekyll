---
layout: post
title: As Monty Python said... "And Now For Something Completly Different"
author: Carlos Muñiz
email: carlos.muniz@crowdint.com
avatar: 89c6420fb99cb01bcf2e3502a7851da2
published: true
---

Perhaps by reading this you may be wondering what is Processing.js?
Well, [Processing.js](http://processingjs.org/) is a JavaScript port of a visualization-oriented language based on Java with the same name Processing (or p5 for short) made by [John Resig](http://ejohn.org), the creator of jQuery.

###First, a little history about p5.

  It was developed by Ben Fry and Casey Reas and it was intended to help electronic arts and visual design communities learn the basis of computer programming in a visual context.

And... what about Processing.js?

  Processing.js is the little sister project of the popular Processing visual programming language, but designed for the web.
  **Processing.js helps you to make your data visualizations, digital art, interactive animations, educational graphs, video games, etc. work using web standards and without any plug-ins.** You only write code using the Processing language, include it in your web page, and Processing.js does the rest. *It's not magic, but almost*.

  Originally developed by Ben Fry and Casey Reas, Processing started as an open source programming language based on Java to help the electronic arts and visual design communities learn the basics of computer programming in a visual context.
  **Processing.js takes this to the next level**, allowing Processing code to be run by **any HTML5 compatible browser**, including current versions of Firefox, Safari, Chrome, Opera, and even the upcoming Internet Explorer 9. *Processing.js brings the best of visual programming to the web, both for Processing and web developers*.

  Ok, so when or where could I use processing?, It's simple, for example: when you need to implement some animation or visual interaction without using Adobe's Flash, or when you want to make new interfaces (like using Arduino) or maybe if you want to put multimedia. That's why I like Processing.js.

  Well, first of all **you'll need the Processing.js** (which could be [downloaded here](http://processingjs.org/content/download/processing-js-1.0.0/processing-1.0.0.min.js)), and __include it on your page__:

{% highlight html %}
<script src="processing-1.0.0.min.js"></script>
{% endhighlight %}

  Then, wherever you want to use it just put it inside a canvas element (note: of course it has to be HTML5 canvas, which I will speak on my next post).

{% highlight html %}
<canvas data-processing-sources="MySuperDuperInteractionFile.pjs."></canvas>
{% endhighlight %}

  **Load your web page, and it will parse, translate, and run your sketch in the browser**... Tadaaaa! =)

  In my experience, I've always hated those basic HELLO WORLD examples so here is a little example which you can try.
  I won't explain all the Processing language, this will just show you a little bouncing ball, nothing great because out there you can find lots of more complex examples.

{% highlight javascript %}

  float frame = 0;         // we start at frame 0
  float framerate = 24;    // our "sketch" will have a framerate of 24 frames per second.

  int ball_x;              // ball x coordinate
  int ball_y;              // ball y coordinate
  int ball_radius = 20;    // ball radius

  void setup() {
    size(200,200);         // set draw area size
    frameRate(framerate);  // set animation framerate
    ball_x = width/2;      // set the initial ball coordinates
    ball_y = ball_radius;  // set the initial ball coordinates
    stroke(#003300);       // set the default shape outline colour
    fill(#0000FF);         // set the default shape fill colour
  }

  void draw() {
    frame++;                       // note that we're one frame further than last time
    float bounce_height = height/2 * abs(sin(PI*frame/framerate));  // compute the ball height for this frame
    float ball_height = height - (bounce_height+ball_radius);       // because the top of the screen is 0, and the bottom is "height",
    background(#FFFFEE);           // clear the drawing area
    ball_y = (int) (ball_height);  // set the new ball y position
    ellipse(ball_x,ball_y,ball_radius,ball_radius);                 // draw the ball
  }

{% endhighlight %}

  What can we see here? If you noticed, we have three main sections, the global variables (nothing to say), the setup method which sets the configuration that will be used in our sketch and the draw method which well... will draw our sketch.

  Wait a minute, Sketch?, yeap... *sketches are the main way to use processing*.
  If you remember Adobe Flash you'll easy understand a sketch because it works similar, they are like small scenes which together make a movie, and just as happens in Processing, you can use several sketches to make more complex things.

  That's all folks! (for now).
  Hope you have enjoyed this small change, as someone said before: Ruby is not the first nor will be the last programming language, it is always good to have alternatives...
