---
layout: post
title:  "Intro to mruby"
date:   2015-12-13 00:22:30
categories: posts
---
I read a post [25,000+ Req/s for Rack JSON API with MRuby](http://lucaguidi.com/2015/12/09/25000-requests-per-second-for-rack-json-api-with-mruby.html). It's really shock me, so I did some research on [mruby](http://www.mruby.org).

### What is mruby?

These are two explaination on it:

  * mruby is the lightweight implementation of the Ruby language complying with part of the ISO standard.([mruby.org](http://www.mruby.org))

  * mruby is an interpreter for the Ruby programming language with the intention of being lightweight and easily embeddable.([wikipedia](https://en.wikipedia.org/wiki/Mruby))

It's still confused. Keep reading and finally you will know what do this mean.

### How to start use it?

It's quite easy to start it, just three steps:

  1. git clone git://github.com/mruby/mruby.git

  2. cd mruby

  3. make (make command will generate bin/ and build/ fold，you should edit build_config.rb depend on your c compiler. You can refer to [Compile mruby](https://github.com/mruby/mruby/blob/master/doc/guides/compile.md))

### How to use it?

I think this is the most important problem, after you figure out this, you will know what is mruby exactly. Please refer to [Executing Ruby code with mruby](http://www.mruby.org/docs/articles/executing-ruby-code-with-mruby.html)

### Conclusion

I think mruby provide the ability to embed ruby code to c code in order to embed to the system support c extention. And this is the alphabet 'm' come from.
