---
layout: post
title: How to code a good CSS
author: Hector Bustillos
email: hector.bustillos@crowdint.com
avatar: fa56812143dd6049ef19d1a8032f21da
publish: false
---

After a while of having worked with CSS, in different projects and with 
different people. I have realized that there is a lack of standards on the 
coding. Like when you have to make some corrections or improvements and open 
the CSS file and you get a thousand lines without any comment.
 
Well I have a couple Ideas of how the CSS should be coded:

## ABC of CSS

- ### Keep it simple.

  Imagine that everytime you write a `<div>` tag somebody will kick you in the nuts,
  painfull huh?... Use only the necessary div tags. Think twice before writing a 
  `<div>` tag, because if you don’t care about it you will get a super complex page.
  Always remeber to keep it simple.

- ### Use the tags for their propose

  I’m always finding some code like this:
  
  {% highlight css %}
   <table>
    <tr>
      <td> Potatos</p>
    </tr>
    <tr>
      <td> Apples</p>
    </tr>
    <tr>
      <td> Watermelons</p>
    </tr>
    <tr>
      <td> Oranges</p>
    </tr>
    <tr>
      <td> Cucumbers</p>
    </tr>
   </table>
  {% endhighlight %}

  
  As you see, its a common list an instead of tag `<ol>` or `<ul>` its using a `<table>`.
  The correct way to do this is:
  
  {% highlight css %}
    <ul>
      <li>Potatos</li>
      <li>Apples</li>
      <li>Watermelons</li>
      <li>Oranges</li>
      <li>Cucumbers</li>
    </ul>
  {% endhighlight %}
  
  you **must** use the correct tag, if you don't know them take a look in [w3schools 
  html reference](http://w3schools.com/tags/default.asp). Here you will find the
  complete list of tags with examples and everything you need..
  
  Also this is important, because, it is easier for the search engines to 
  identify your page and understand its code. Remember the search engines 
  are like a blind users

- ### Use comments

  Try to comment your CSS. It is easier for others to see the comments and 
  know to which part the code corresponds to.
  
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

- ### Avoid absolute positions

  When you start making CSS, you find a "easy" way to put every element on
  the right place, the absolute position. This is a very common, yet wrong 
  method to align your elements. sometimes when you use the absolute positions, 
  you will get a weird behavior after a window resize or in the different 
  browser or screens, for you help try to avoid it, and use margins and 
  padding to do the work.  Always remember [the box model](http://www.w3.org/TR/CSS21/box.html)..
  



## Ok.. first steps
  
### Reset your CSS

  Did you ever realize the tittle differences between the browser when you put 
  a text into a `<div>` or inside `<p>` you will see a couple of differences in
  how the browser render the elements.

  ![Diferent renders](/images/2011/renders.jpg "Renders")

  This happens because the browsers have different ways to render the element 
  of a page. An easy way to avoid this resetting of your CSS,  is a simple 
  technique where you put all the style to 0/none by default.  You can get this 
  with only applying the [Eric Meyer's css reset](http://meyerweb.com/eric/tools/css/reset/index.html),
  you can download it and put it on your public folder, or use directly by the url.
  
  
### styles for tags first

  I prefer to write styles for tags first, it’s something like starting from 
  the general to the particular. In other words, make it general, and use 
  exceptions only when is really needed.

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
  
### Be modular:

 Remember to separate your CSS like modules, first the body definitions, 
 then the header definitions, etc. 
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
  lines. And you end up with like 10 different files with a thousand lines on 
  each one.

  Well if you end up having that, you are doing something really wrong.

  Take your time to write the CSS, once you get the expected result, see 
  your CSS and try to clean the code.
  
  > Perfection is achieved, not when there is nothing more to add, but when there is nothing left to take away.
#### Antoine de Saint-Exupery *French writer (1900 - 1944)*
    
  There's always a way to make your code look better. If you take care about 
  it you will get a maintainable code, very easy to read.
  
### Cross-browsering **ALWAYS**

  I wrote "always"  with caps and bold, because it is something that we 
  forget very often. We write and write CSS lines, and we test on Firefox or 
  chrome because we love those browsers, and we are very happy about the way 
  our site looks, and after a couple of days of coding, you start thinking 
  about how it will look on IE… the awfulness  all web developers fear to 
  see their work on. You take a test in IE and you realize that the entire 
  site is messed up, the whole layout is broken.
 
  You don’t know where to start but you do it anyway.  
  Begin doing some changes here and there and you finish with tons 
  of patches or writing a new CSS file just for IE.

  That’s not necessary if you take into consideration what you are doing right 
  on the first step. If you wrote 2 lines, verify in all browsers that your 
  style is working right. Doing this you will end with a clean code and 
  compatible with all browsers.
  
### Rember the W3C

  Always keep in my the standards and validate your site using the [CSS](http://jigsaw.w3.org/css-validator/) and 
  [HTML](http://validator.w3.org/) validators, if your code passes the validation, your site will 
  be readable for all the browser for sure.


### My final words

  All suggestions regarding good CSS writing, are about taking care of the 
  details.  Details make the difference, you only have to put some attention 
  and code in the right way.  Remeber that sometimes your code is gonna be 
  seen by other developers and you have to make it clear for them and for the 
  browsers too.

  Also remember the accessibility of your site, think of the people at all 
  times… people with some illness or different capacities, like the blind 
  people and so on. You can check the [Web Content Accessibility Guidelines](http://www.w3.org/TR/WCAG10/) 
  and try to at least complete the guides for Level A, the visitors of your 
  site will be grateful about this.

  **And for God’s sake... Code clean!!!!**


