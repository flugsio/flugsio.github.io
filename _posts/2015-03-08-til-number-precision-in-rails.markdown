---
layout: post
title:  "TIL: number precision in rails"
date:   2015-03-08 17:30
categories: code
---

Recently when building a hobby project for my mother,
I wanted to get rid of unnecessary zeros from numbers;
in one usage the numbers always ended with 0.0 for the latest 300 rows
(numbers like 20.0, 30.0, 50.0, 80.0),
and in another it was 1.0 about 80% of the time and 0.5 for the rest.

I hastily searched the [rails api](http://api.rubyonrails.org)
and found a suitable method
[`number_to_human(number, options = {})`][rails-number_to_human].
It seemed to work great at first,
at least in the few cases the site used.

Here are some of them:

<h4 class="filename">rails console</h4>
{% highlight ruby %}
irb(main):001:0> include ActionView::Helpers::NumberHelper
=> Object
irb(main):002:0> def sig(n)
irb(main):003:1>   number_to_human(n, precision: 1,
irb(main):003:2*                   strip_insignificant_zeros: true)
irb(main):005:1> end
=> :sig
irb(main):006:0> sig(30.0)
=> "30"
irb(main):007:0> sig(90.0)
=> "90"
irb(main):008:0> sig(0.5)
=> "0.5"
irb(main):009:0> sig(1)
=> "1"
{% endhighlight %}

The first bug
-------------

The code felt simple enough
and I didn't even bother to test it properly.
But then we noticed something,
380.0 turned into 400.
I ran the code in the console to see what was passed into the function,
and got this `#<BigDecimal:3cdf908,'0.38E3',9(18)>`.

My first guess was that it happened because of the
[scientific e notation](http://en.wikipedia.org/wiki/Scientific_notation#E_notation)
and that the type was BigDecimal because i summed up multiple numbers.
I thought it got rounded to 0.4 before multiplied with 1000,
that didn't really make sense and I should've test to send 380 to the function in literal notation.

I felt that I needed to make a bug-report,
but then I would've need to research it a bit more,
and I didn't have the time so I just reverted the code.

The second bug
--------------

In the other place,
1.5 turned into 2.
I though `precision: 1` should keep 1 decimal place not round it away.
This time it felt really wrong, 
I went back to the documentation and found the problem.

{% highlight ruby %}
number_to_human(number, options = {})
  :significant - If true, precision will be the # of significant_digits.
                 If false, the # of fractional digits (defaults to true)
{% endhighlight %}

Not only is the default for strip_insignificant_zeros true,
but significant should be false if I want precision to control only fractional digits.
This makes sense as this function is used to humanize and approximate big numbers,
which is not what I was looking to do.

This time I also found number_with_precision,
which already defaults to significant: false,
but instead has strip_insignificant_zeros: false.

{% highlight ruby %}
number_with_precision(
  380.0,
  strip_insignificant_zeros: true,
  precision: 1
)
=> "380"
{% endhighlight %}

Lessons learned
---------------

When searching documentation
try to not skim through the things your going to use
for the first time.
Take a moment and look up a bit more than what you need.
This will help save time and frustration in the future.
It's good to know what your tools can do.

When not doing TDD fully,
put some real data into the dev environment to help spot issues faster.

When investigating a bug, think (guess) less and look more.

[rails-number_to_human]: http://api.rubyonrails.org/classes/ActionView/Helpers/NumberHelper.html#method-i-number_to_human
