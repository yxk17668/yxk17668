---
title: helper-live2d
date: 2019-06-06 23:39:19
tags:
  - hexo
	- helper-live2d
	- yilia
toc: true
---

## helper-live2d插件

>博客根目录选择cmd命令窗口或者git bash 输入以下代码，安装插件
```
npm install --save hexo-helper-live2d
```

## 下载模型

>作者动画展示网站[helper-live2d](https://huaji8.top/post/live2d-plugin-2.0/);任选下载

```
live2d-widget-model-chitose
live2d-widget-model-epsilon2_1
live2d-widget-model-gf
live2d-widget-model-haru/01 (use npm install --save live2d-widget-model-haru)
live2d-widget-model-haru/02 (use npm install --save live2d-widget-model-haru)
live2d-widget-model-haruto
live2d-widget-model-hibiki
live2d-widget-model-hijiki
live2d-widget-model-izumi
live2d-widget-model-koharu
live2d-widget-model-miku
live2d-widget-model-ni-j
live2d-widget-model-nico
live2d-widget-model-nietzsche
live2d-widget-model-nipsilon
live2d-widget-model-nito
live2d-widget-model-shizuku
live2d-widget-model-tororo
live2d-widget-model-tsumiki
live2d-widget-model-unitychan
live2d-widget-model-wanko
live2d-widget-model-z16
```
>在hexo博客根目录选择cmd命令窗口或者git bash 输入以下代码

```
$ npm install live2d-widget-model-hijiki
```

## 添加配置

>_config.yml 文件添加配置

```
live2d:
  enable: true
  scriptFrom: local
  pluginRootPath: live2dw/
  pluginJsPath: lib/
  pluginModelPath: assets/
  tagMode: false
  debug: false
  model:
    use: live2d-widget-model-hijiki
  display:
    position: right
    width: 150
    height: 300
  mobile:
    show: true
```