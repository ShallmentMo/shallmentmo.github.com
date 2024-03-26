---
layout: post
title:  "在 ASDF 里面用遗留版本的 PHP(MacOS)"
date:   2024-03-26 15:26:30 +0800
categories: posts
---

# 在 ASDF 里面用遗留版本的 PHP(MacOS)

## 什么是 ASDF

ASDF 是一个版本管理工具，类似于 NVM，但是支持更多的语言和工具。它的官方网站是 [asdf-vm.com](https://asdf-vm.com/)。同时它也支持 PHP，插件用 [asdf-php](https://github.com/asdf-community/asdf-php) 来管理。

## 遗留版本的 PHP

在某些情况下，我们需要用到遗留版本的 PHP，比如某些项目还在用 PHP 7.2，这时候我们就需要用到 ASDF 来管理多个版本的 PHP。但是现在的情况是用 asdf 安装 php 8.1 是成功的，但是用 asdf 安装 php 7.2.34 是会出现杂七杂八的问题(主要 openssl icu4c 这些依赖)。

## 解决方案

之前我是用 brew 安装 php 7.2.34 的，但是新的项目会用到 8.1，所以我转向了 asdf。但是我可以用 asdf 来装 backport 的 php。

1. 根据 [php 7.2 formula](https://github.com/shivammathur/homebrew-php/blob/master/Formula/php%407.2.rb) 更改 asdf-php 的依赖。我这次的改动主要是把 openssl@1.1 这个依赖改成 openssl@3
2. 修改 asdf-php 的 `get_download_url` 把下载的链接改成 `https://github.com/shivammathur/php-src-backports/archive/7.2.34.tar.gz` 就是 backport 的 php 7.2.34 版本
3. 修改 asdf-php 的 `install` 方法，把解压后的文件夹改成 `php-src-backports-7.2.34`，这样 asdf-php 就能找到正确的文件夹了

## 参考

- [asdf-vm.com](https://asdf-vm.com/)
- [homebrew-php](https://github.com/shivammathur/homebrew-php)
- [php src backports](https://github.com/shivammathur/php-src-backports)
