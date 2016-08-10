---
layout: post
title:  "use phantomjs to create image"
date:   2016-08-10 22:31:30 +0800
categories: jekyll update
---

In last two week I was working on a project which need to generate image. But we haven't found some good ways(If you know some good ways, please tell me).Until in these days, I came up with an idea -- using screen capture feature in [phantomjs](http://phantomjs.org/) to create image. I know html and css, so I can build the webpage and use phantomjs to render it to image. I try it tonight. It turn out to be OK. My first thought is using this to build a package. But it's some lines of code, so I prefer writing it down as a post.

*1. Install phantomjs globally

{% highlight bash %}
npm install -g phantomjs
{% endhighlight %}

*2. Build your webpage, whatever you like

*3. Write down this code below to `image.js`

{% highlight javascript %}
var page = require('webpage').create();

// please set your size here
page.viewportSize = {
  width: 400,
  height: 600
};

// the url can be anything you like
page.open('file:///xxx.html', function() {
  page.render('xxx.png');
  phantom.exit();
});
{% endhighlight %}

*4. Run this command to create image

{% highlight bash %}
npm install -g phantomjs
{% endhighlight %}
