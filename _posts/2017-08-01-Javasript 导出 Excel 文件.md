---
layout: post
title:  "Javascript 导出 Excel 文件"
date:   2017-08-01 22:47:30 +0800
categories: posts
---

最近在处理前端用 Javascript 导出 Excel 文件，一开始是用 [xlsx](https://github.com/SheetJS/js-xlsx) 这个库，这个库功能强大，如果需要支持一些样式的话，就需要 [xlsx-style](https://github.com/protobi/js-xlsx)，xlsx-style 是 fork 了 xlsx 的 repo 下处理的。

但是现在的问题是需要支持导出几十万的数据，xlsx 就变得特别慢，至少我还没用它成功导出过几十万的数据。所以我需要换别的方式来处理这个问题。以下三个是我找到的其它库：

* [exceljs](https://github.com/guyonroche/exceljs) 这个库因为在前端引入时依赖会报错，所以放弃了
* [xlsx-populate](https://github.com/dtjohnson/xlsx-populate) 这个库整体是很好的，速度上也可以，但是不能设置单元格的格式，而需求对这点还是有要求的
* [zipcelx](https://github.com/egeriis/zipcelx) 这个库是三个里面最简单的，虽然没有 xlsx-populate 里面功能，可是能够设置单元格格式（虽然只有 string 跟 number）。

最后我选择了 zipcelx，主要是简单，代码简单看就能明白，最主要的是支持设置单元格格式。但是在用 zipcelx 遇到另一个问题，就是数据量足够多的时候，会出现 `Uncaught RangeError: Invalid array length`，字符串长度不够的问题。所以只能把数据拆成不同的表来解决这个问题。这是 zipclex 简单的好处就体现了，我只需要改一下 template 跟参数，就可以支持拆分不同的表了。

PS：个人觉得 zipcelx 还有一个问题是不是返回一个 Promise，所以时序上不是很好操作，所以我提交了一个 MR 给作者，不过似乎没人理我 😂
