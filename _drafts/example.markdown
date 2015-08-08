---
layout: post
title:  "Welcome to Jekyll!"
date:   2015-02-22 15:33:56
categories: life code tool turf chess
---
You'll find this post in your `_drafts` directory - use this post for usage examples and collection of categories. Edit posts in this and the `_posts` directory then re-build (or run with the `-w` switch and --drafts) to see your changes!

Header 1
========

Header 2
--------

To add new posts, simply add a file in the `_posts` directory that follows the convention: YYYY-MM-DD-name-of-post.ext.

Jekyll also offers powerful support for code snippets:

<h4 class="filename">test.rb</h4>

{% highlight ruby %}
#
# put some description here
# and next line
#
class Game < Screen
  attr_reader :map, :space

  def initialize(folder)
    super('Kanazapanoid', 1280, Ration)

    # put the score here
    @score = 0
    @font = Font.new(self, Gosu::default)

    @map = VectorMap.new self
    @map.open 'test'

    @content_dir = folder
  end

  def find_post(entry_id)
    result = nil
    each_post { |post|
      result = post
      break if post.entry_id == entry.id
    }
    result
  end
end

print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll's GitHub repo][jekyll-gh].

[jekyll-gh]: https://github.com/mojombo/jekyll
[jekyll]:    http://jekyllrb.com
