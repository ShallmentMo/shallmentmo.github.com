---
layout: post
title:  "Bitmaps of Randomness in Ruby"
date:   2016-05-28 21:41:30 +0800
categories: jekyll update
---
Today I heppend to find [random.org](https://www.random.org), so I got [Statistical Analysis](https://www.random.org/analysis/) and [Pseudo-Random vs. True Random](http://boallen.com/random-numbers.html). Then why not try it using Ruby? So I do the test, it seems that Ruby do just fine. Is there another way to test?

These is are the code I write:
{% highlight ruby %}
require 'chunky_png'
require 'securerandom'

SIZE = 300

png = ChunkyPNG::Image.new(SIZE, SIZE, ChunkyPNG::Color::BLACK)
(0...SIZE).each do |i|
  (0...SIZE).each do |j|
    png[j, i] = ChunkyPNG::Color::WHITE if rand(2) == 1
  end
end
png.save('rand.png', interlace: true)

png = ChunkyPNG::Image.new(SIZE, SIZE, ChunkyPNG::Color::WHITE)
(0...SIZE).each do |i|
  (0...SIZE).each do |j|
    png[i, j] = ChunkyPNG::Color::BLACK if SecureRandom.random_number(2) == 0
  end
end
png.save('securerandom.png', interlace: true)

mruby_arr = [0, 1..0, 1] # it seems that mruby doesn't has something like ChunkyPNG, so this is an 300 * 300 array generated using mruby

png = ChunkyPNG::Image.new(SIZE, SIZE, ChunkyPNG::Color::BLACK)
mruby_arr.each_with_index do |num, index|
  png[index / SIZE, index % SIZE] = ChunkyPNG::Color::WHITE if num == 1
end
png.save('mruby_rand.png', interlace: true)

{% endhighlight %}

Here are what I got:

![rand.png](/images/rand.png)
rand.png

![securerandom.png](/images/securerandom.png)
securerandom.png

![mruby_rand.png](/images/mruby_rand.png)
mruby_rand.png

So far so good. I like ruby. Thanks for [chunky_png](http://chunkypng.com/).
