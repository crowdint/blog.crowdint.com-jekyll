---
layout: post
title: First contact with regular expressions
author: Francisco Guzm&aacute;n
email: francisco.guzman@crowdint.com
avatar: c000ffd0c4ed3e23d09cae624d24b525
---

If you're looking for a way to save yourself some lines of code along with some time thinking about match cases, regular expressions are your friend. Mainly used to recognize patterns, a regular expression is a way to specify a pattern of characters to be searched within a string.

They are built within forward slashes and they even have their own class

{% highlight ruby %}
/expression/.class
#=> Regexp
{% endhighlight %}

##Taxonomy of a Regular Expression

If you've seen a regular expression before, they must look scary to you; in fact, that's the reason why many developers won't take the time to learn about them. Underneath such a scary mob of characters, there a extremely simple structure:

{% highlight ruby %}
/expression/options
{% endhighlight %}

Also, in Ruby you can express a regular expression in two alternate ways:

{% highlight ruby %}
%r{expression}options

Regexp.new('pattern' [, options])
{% endhighlight %}

The method *match* and the operator *=~* can be used to verify if a string matches a regular expression:

{% highlight ruby %}
match1 = /Example/.match("Example of a Regexp")
#=> "Example"
puts match1.class
#=> MatchData
{% endhighlight %}

When using the method *match*, the returned value is the matched string or *nil* if no match was found. The class of the object that received the assignment is *MatchData*.

##Building regular expressions

To start building our own regular expressions, there is a couple of lists we have to become familiar with:

### 1. Special Characters

Some characters have a special meaning in regular expressions, and must be escaped to prevent them from processing and be able to search for them. The escape character used is **\\**.

####Special characters list

^  
$  
?  
.  
/  
\\  
[  
]  
{  
}  
(  
)  
+  
\*  	

### 2. Types of expressions

The most common patterns we can specify in a regular expressions are included in the following table:

<table>
	<tr>
		<th>expression</th>
		<th>meaning</th>
  </tr>
  <tr>
		<td>.</td>
		<td>any character</td>
	</tr>
  <tr>
		<td>[ ]</td>
		<td>specify a range (e.g. [a-z] means a letter from a to z)</td>
	</tr>
  <tr>
		<td>\w</td>
		<td>letter or digit; same as [0-9A-Za-z]</td>
	</tr>
  <tr>
		<td>\W</td>
		<td>any character but letter or a number</td>
	</tr>
  <tr>
		<td>\s</td>
		<td>space character; same as [ \t\n\r\f]</td>
	</tr>
  <tr>
	  <td>\S</td>
	  <td>any character but a space space char</td>
	</tr>
  <tr>
	  <td>\d</td>
	  <td>a number; same as [0-9]</td>
	</tr>
  <tr>
	  <td>\D</td>
	  <td>any character but a number</td>
	</tr>
  <tr>
	  <td>\b</td>
	  <td>backspace (0x08), only within a range</td>
	</tr>
  <tr>
	  <td>\b</td>
	  <td>word limit, if NOT within a range</td>
	</tr>
  <tr>
	  <td>\B</td>
	  <td>not word limit</td>
	</tr>
  <tr>
	  <td>*</td>
	  <td>zero or more matches of the preceding</td>
	</tr>
  <tr>
	  <td>+</td>
	  <td>one or more matches of the preceding</td>
	</tr>
  <tr>
	  <td>^</td>
	  <td>matches the beginning of a line</td>
	</tr>
  <tr>
	  <td>$</td>
	  <td>matches the end of a line</td>
	</tr>
	<tr>
		<td>\A</td>
		<td>matches the beginning of the string</td>
	</tr>
	<tr>
		<td>\z</td>
		<td>matches the end of the string (\Z will do the same unless the string ends with a '\n', in which case it matches just before the '\n')</td>
  <tr>
	  <td>{m[,n]}</td>
	  <td>at least 'm', topping 'n' matches of the preceeding ('n' is optional)</td>
	</tr>
  <tr>
	  <td>?</td>
	  <td>at least one match of the preceeding (same as {1})</td>
	</tr>
  <tr>
	  <td>( )</td>
	  <td>group expressions</td>
	</tr>
	<tr>
		<td>|</td>
		<td>logical operator 'or', in [a|b] looks for a OR b</td>
	</tr>
</table>

##Common examples

###The e-mail address

This is a very common pattern example (it's the one that you get by default in [Rubular]("http://rubular.com")).

{% highlight ruby %}
/\A([^@\s]+)@((?:[-a-z0-9]+\.)+[a-z]{2,})\Z/
{% endhighlight %}


First, '\A' makes sure to match the string against the start of string; then ([^@\s]+) groups the recipient's name, avoiding @ character and space characters.

After the recipient's name, we have the @ character and then we have a group with two groups inside: (?:[-a-z0-9]+\.) which only matches to alpha-numeric characters plus a single dot at least once; and then +[a-z]{2,}, at least two alpha-numeric characters for the domain.

Finally, the '\Z' makes sure to match the end of line

###The phone number

{% highlight ruby %}
/\((\d{3})\)\s+(\d{3})-(\d{4})/
{% endhighlight %}

First, \((\d{3})\)\s+ will look for a group of three numeric characters enclosed by parenthesis and a blank space at least once; then (\d{3})- searches for three numeric characters and dash, and finally (\d{4}) will look for the final four numeric characters.

##Where to look for more information

The star of these links is [Rubular]("http://rubular.com")
but there are some few more useful links to look at:
<http://www.regular-expressions.info/ruby.html>
<http://www.rubyist.net/~slagell/ruby/regexp.html>
<http://www.ruby-doc.org/docs/ProgrammingRuby/html/language.html#UJ>
<http://www.ruby-doc.org/docs/ProgrammingRuby/html/ref_c_regexp.html#Regexp.new>
<http://www.troubleshooters.com/codecorn/ruby/basictutorial.htm>

Enjoy!
