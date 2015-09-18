---
layout: post
title: "Java解析Html"
date: 2015-03-29 13:20:36 +0800
comments: true
categories: [Java]
keywords: [Java, Html, Parser]
description: java解析html
---


最近用到了Java解析Html的一个库Jsoup, 这儿是[官网](http://jsoup.org/), 在此分享给大家，有这方面需要的朋友可以试一试。

有三个类需要我们了解，分别是Document，Elements，Element

大至用法有两步<br>

#第一步:加载html,，这儿提供两种方式，一种是从本地加载，一种是从网上直接加载。
##从本地加载:

```
String html = "YOU HTML STRING";
Document doc = Jsoup.parse(html);
```
也可以直接从文件加载

```
File input = new File("/tmp/input.html");
Document doc = Jsoup.parse(input, "UTF-8", "http://example.com/");
```

##通过url从网络加载

```
Document doc = Jsoup.connect("http://en.wikipedia.org/").get();
String title = doc.title();
```
上面是通过http的get方法，下可以通过post来获取

```
Document doc = Jsoup.connect("http://example.com")
  .data("query", "Java")
  .userAgent("Safari")
  .cookie("auth", "token")
  .timeout(3000)
  .post();
```

#第二步:定位元素

##通过定义的api定位无素

定位body

```
String html = "<div><p>Lorem ipsum.</p>";
Document doc = Jsoup.parseBodyFragment(html);
Element body = doc.body();
```

定位标签

```
Element content = doc.getElementById("content");
Elements links = content.getElementsByTag("a");
for (Element link : links) {
  String linkHref = link.attr("href");
  String linkText = link.text();
}
```
常用的API有

查找API：

```
getElementById(String id)
getElementsByTag(String tag)
getElementsByClass(String className)
getElementsByAttribute(String key) (and related methods)
兄弟关系的:siblingElements(), firstElementSibling(), lastElementSibling(); nextElementSibling(), previousElementSibling()
父子关系的: parent(), children(), child(int index)
```
值操作API:

```
attr(String key) to get and attr(String key, String value) to set attributes
attributes() to get all attributes
id(), className() and classNames()
text() to get and text(String value) to set the text content
html() to get and html(String value) to set the inner HTML content
outerHtml() to get the outer HTML value
data() to get data content (e.g. of script and style tags)
tag() and tagName()
```

修改API

```
append(String html), prepend(String html)
appendText(String text), prependText(String text)
appendElement(String tagName), prependElement(String tagName)
html(String value)
```

##通过select语法定位元素

这个不好用文字表达，直接看[官网](http://jsoup.org/cookbook/extracting-data/selector-syntax)文档吧.


>时间仓促，难免有不少错误，还往指正。