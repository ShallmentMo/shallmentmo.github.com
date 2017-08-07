---
layout: post
title:  "Lua 的 makeseed 方法"
date:   2017-08-07 21:57:30 +0800
categories: posts
---

这些都只是我的理解，如果发现问题，欢迎提出 issue。

Lua 5.3.4 在 new state 的时候会调用一个 [makeseed](#makeseedlstatec81) 方法来生成给 global state 的 seed。

##### makeseed(lstate.c#81)
{% highlight c %}
static unsigned int makeseed (lua_State *L) {
  char buff[4 * sizeof(size_t)];
  unsigned int h = luai_makeseed();
  int p = 0;
  addbuff(buff, p, L);  /* heap variable */
  addbuff(buff, p, &h);  /* local variable */
  addbuff(buff, p, luaO_nilobject);  /* global variable */
  addbuff(buff, p, &lua_newstate);  /* public function */
  lua_assert(p == sizeof(buff));
  return luaS_hash(buff, p, h);
}
{% endhighlight %}

[luai_makeseed](#luai_makeseedlstatec40) 这个地方是开放可以允许用户定义自己的 seed 方法，否则的话，Lua 会默认用 `time(NULL)` 返回当前的 timestamp。

##### luai_makeseed(lstate.c#40)
{% highlight c %}
/*
** a macro to help the creation of a unique random seed when a state is
** created; the seed is used to randomize hashes.
*/
#if !defined(luai_makeseed)
#include <time.h>
#define luai_makeseed()   cast(unsigned int, time(NULL))
#endif
{% endhighlight %}

然后我们会留意到 [addbuff](#addbufflstatec73) 方法，这个是我看来是 Lua 生成随机种子最重要来源，它依赖于内存分配的随机性，需要系统支持，不过一般都支持。从注释可以看出，`makeseed` 方法会用 L（heap variable），h（local variable），luaO_nilobject（global variable），lua_newstate（public function）它们的地址来构成 buff。

##### addbuff(lstate.c#73)
{% highlight c %}
/*
** Compute an initial seed as random as possible. Rely on Address Space
** Layout Randomization (if present) to increase randomness..
*/
#define addbuff(b,p,e) \
  { size_t t = cast(size_t, e); \
    memcpy(b + p, &t, sizeof(t)); p += sizeof(t); }
{% endhighlight %}

最后，再调用 [luaS_hash](#luas_hashlstringc49) 来哈希出一个 int。这个方法有个关于 Shift-Add-XOR hash 的哈希算法，可以参考 [jsw_tut_hashing](http://www.eternallyconfuzzled.com/tuts/algorithms/jsw_tut_hashing.aspx)，谢谢 Lua 社区告诉我这个哈希算法。

##### luaS_hash(lstring.c#49)
{% highlight c %}
unsigned int luaS_hash (const char *str, size_t l, unsigned int seed) {
  unsigned int h = seed ^ cast(unsigned int, l);
  size_t step = (l >> LUAI_HASHLIMIT) + 1;
  for (; l >= step; l -= step)
    h ^= ((h<<5) + (h>>2) + cast_byte(str[l - 1]));
  return h;
}
{% endhighlight %}
