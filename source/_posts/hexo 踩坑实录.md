---
title: Hexo 踩坑实录
date: 2025-2-23 21:16:13
tags: [Hexo]
categories: [Hexo]
description: Hexo 踩坑实录
---

# 踩坑

## 0. 背景

版本信息


> hexo: 7.3.0
> hexo-cli: 4.3.2
> os: darwin 23.5.0 14.5
> node: 23.7.0
> acorn: 8.14.0
> ada: 2.9.2
> amaro: 0.3.0
> ares: 1.34.4
> brotli: 1.1.0
> cjs_module_lexer: 1.4.1
> cldr: 46.0
> icu: 76.1
> llhttp: 9.2.1
> modules: 131
> napi: 10
> nbytes: 0.1.1
> ncrypto: 0.0.1
> nghttp2: 1.64.0
> openssl: 3.4.1
> simdjson: 3.10.1
> simdutf: 6.0.3
> sqlite: 3.47.2
> tz: 2024b
> undici: 6.21.1
> unicode: 16.0
> uv: 1.50.0
> uvwasi: 0.0.21
> v8: 12.9.202.28-node.12
> zlib: 1.2.12


## 1. 字数、阅读时长失效
文章的"字数、阅读时长"字样存在，但是没有值或者 Nan

原因：<font color= #871F78> _hexo-symbols-count-time_ </font> 的版本问题

解决方案：
```
npm i hexo-symbols-count-time@latest
# or
npm i hexo-symbols-count-time@0.6.0
# or
rm package-lock.json
npm i hexo-symbols-count-time
# or
npm un hexo-symbols-count-time
npm i hexo-symbols-count-time

```

## 2. 侧边栏不能居中
原来写的侧边栏是可以居中的，这次更新版本不能居中...

没找到设置的地方，自己改了改，不太会前端...

> .../source/css/_common/outline/sidebar/sidebar.styl

修改sidebar-inner的样式
```css
.sidebar-inner {
  color: $grey-dark;
  padding: $sidebar-padding 10px;
  text-align: center;
  position: relative;
  top: 20%;
}

```

不太行，文章里的侧边栏也被改了...

## 3. 一些莫名其妙的报错
比如 hexo deploy 时
很大概率是版本问题，删掉重新下载
```
npm un XX 
```