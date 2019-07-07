---
title: yilia主题
date: 2019-06-06 17:08:03
tags:
	- hexo
	- yilia
toc: true
---
##Hexo添加文章目录

### 文件配置
>我们需要编辑文章显示页面的模版，如下文件
``` html
	themes/landscape/layout/_partial/article.ejs
```
>在文件中找到对应的关键代码
``` html
	<%- post.content %>
```
>并在这一行之前加入如下代码：
``` html
<!-- Table of Contents -->
<% if (!index && post.toc){ %>
<div id="toc" class="toc-article">
<strong class="toc-title">文章目录</strong>
<%- toc(post.content) %>
</div>
<% } %>

```
>文章开头的front-matter中添加一句toc: true属性

### CSS样式添加

>指定文件目录修改样式
``` html
themes/landscape/source/css/_partial/article.styl
```
>代码如下：
``` html
/*toc*/
.toc-article
    background #eee
    border 1px solid #bbb
    border-radius 10px
    margin 1.5em 0 0.3em 1.5em
    padding 1.2em 1em 0 1em
    max-width 28%

.toc-title
    font-size 120%

#toc
    line-height 1em
    font-size 0.9em
    float right
.toc
    padding 0
    margin 1em
    line-height 1.8em
    li
    list-style-type none

.toc-child 
    margin-left 1em

```
### 注意

>由于Hexo使用的是stylus预处理器，所以CSS代码要注意缩进，不然就报错了
