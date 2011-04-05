---
layout: post
title: How to write good CSS
author: Hector Bustillos
email: hector.bustillos@crowdint.com
avatar: fa56812143dd6049ef19d1a8032f21da
publish: false
---

After a while of working with CSS, in different projects and with
different people, I have realized that there is a lack of standards for
it. Like, when you have to make some corrections or improvements and open
the CSS file and you get a thousand lines of code with no comments at
all.

Well, I have a couple Ideas of how CSS should be coded:

## The ABC of CSS

### Keep it simple.

Imagine that every time you write a `<div>` tag somebody kicks you in the nuts,
painful huh?... Use only the necessary div tags. Think twice before writing a
one, because if you don’t care about it you will get a super complex page.
Always remember to keep it simple.

### Use HTML tags for their propose

I'm always finding some code like this:

  {% highlight css %}
   <table>
    <tr>
      <td>Potatoes</p>
    </tr>
    <tr>
      <td>Apples</p>
    </tr>
    <tr>
      <td>Watermelons</p>
    </tr>
    <tr>
      <td>Oranges</p>
    </tr>
    <tr>
      <td>Cucumbers</p>
    </tr>
   </table>
  {% endhighlight %}

As you see, it's a common list an instead of using `<ol>` or `<ul>` its using a `<table>`.

A better way to do this would be:

  {% highlight css %}
    <ul>
      <li>Potatoes</li>
      <li>Apples</li>
      <li>Watermelons</li>
      <li>Oranges</li>
      <li>Cucumbers</li>
    </ul>
  {% endhighlight %}

You **must** use the correct tag; if you are not familiar with all of them
take a look at [w3schools html reference](http://w3schools.com/tags/default.asp).
Here you will find the complete list of tags with examples and everything you need.

Also, this is important because, it is easier for search engines to 
identify your page and understand its code. Remember that search engines 
are like a blind users.

### Use comments

Try to put comments on your CSS. It is easier for others to see the comments and
know what the styles are being used for.

{% highlight css %}
  // Common tags styles
  a {
    text-transform: uppercase;
  }

  // Header styles
  #header{
    background-color: #FFFFFF; 
  }

  #header a{
    color: black;
  }
  //end

  // Left Menu styles
  #left-menu{
    font-size: .8em; 
  }
  //end

{% endhighlight %}

### Avoid absolute positions

When you start making CSS, you can find it "easy" to put every element on
the right place using absolute position. This is a very common, yet
incorrect
method to align your elements. Sometimes when you use absolute positions,
you will get weird behavior after a window resize or in different
browsers or screens, just try to avoid it and use margins and 
paddings to do the work.  Always remember [the box model](http://www.w3.org/TR/CSS21/box.html).

## Ok, First steps

### Reset your CSS

Did you ever realize the huge differences between the browser when you put
a text into a `<div>` or inside `<p>`? You will always face a couple of differences in
how each browser renders the elements.

![Diferent renders](/images/2011/renders.jpg "Renders")

This happens because the browsers have different ways to render the elements
of a page. An easy way to avoid this is by resetting your CSS, a simple 
technique where you put all the style to 0/none by default. 
You can achieve this by applying [Eric Meyer's css reset](http://meyerweb.com/eric/tools/css/reset/index.html),
download it and put it on your public folder, or link to the url
directly.

### Styles for tags first

I prefer to write styles for tags first, it’s something like starting from 
the general to the particular. In other words, make it general, and use 
exceptions only when it is really necessary.

{% highlight css %}
  body{
    font-family: Arial;
  }

  h1{
    font-size: 1.2em;
  }

  .secondary_pages h1{
    font-size. 1em;
    font-family: Verdana;
  }

{% endhighlight %}

### Be modular

Remember to separate your CSS like modules, first, the body definitions,
then the header definitions, and so on.
Also separate them by files like **main.css** and **form.css**.

{% highlight css %}
  // Header CSS
  #header{
    background-color: #FFFFFF;
  }
  #header a{
    color: black;
  }

  // Left Menu
  #left-menu{
    font-size: .8em; 
  }
  a{
    text-transform: uppercase;
  }

{% endhighlight %}

###  Do you feel like I am re-writing the whole bible? (Old and new testament!)

Sometimes you start to write CSS and then you get velocity, or you have a 
deadline, then you get yourself coding like a mad man. Tons and tons of CSS 
lines. Then, you end up with like 10 different files with a thousand lines on 
each one.

Well if you end up having that, you are doing it completely wrong.

Take your time to write the CSS, once you get the expected result,
take a look at your code and try to clean the clean it.

> Perfection is achieved, not when there is nothing more to add, but when there is nothing left to take away.
>
>
> #### Antoine de Saint-Exupery *French writer (1900 - 1944)*

There's always a way to make your code look better. If you take care about 
it you will get maintainable, very easy to read and understandable code.

### Cross-browser testing **ALWAYS**

I highlighted "always" with caps and bold, because it is something that we 
forget about very often. We write and write CSS lines, and we test on Firefox or 
Chrome because we love those browsers, and we are very happy about the way 
our site looks, then after a couple of days of coding, you start thinking 
about how it will look on IE... that awfulness all web developers fear to 
see their work on. You test in IE and you realize that the entire 
site is messed up, the whole layout is broken.

You don’t know where to start but you do it anyway.
Begin making changes here and there and you finish with tons 
of patches or writing a new CSS file just for IE.

That’s not necessary if you take into consideration what you are doing right 
from the first step. If you wrote 2 lines, verify in all browsers that your 
style is working right. By doing this you will end with clean code and 
compatible with all browsers.

### Remember the W3C

Always keep in mind the standards and validate your site using the
[CSS](http://jigsaw.w3.org/css-validator/) and
[HTML](http://validator.w3.org/) validators. If your code passes the validation, your site will 
be readable for all browsers for sure.

### My final words

All suggestions regarding good CSS writing, are about taking care of the 
details.  Details make the difference, you only have to put some attention 
and code in the right way.  Remeber that sometimes your code is gonna be 
seen by other developers and you have to make it clear for them and for the 
browsers too.

Also remember the accessibility of your site, think of the people at all 
times... people with some illness or different capacities, like the blind 
people and so on. You can check the [Web Content Accessibility Guidelines](http://www.w3.org/TR/WCAG10/) 
and try to at least complete the guides for Level A, the visitors of your 
site will be grateful about this.

**And for God’s sake... Code clean!!!!**

