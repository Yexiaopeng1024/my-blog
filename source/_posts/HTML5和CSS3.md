---
title: HTML5和CSS3
date: 2019-12-1 17:18:19
tags: [HTML,CSS]
categories:
---
&emsp;&emsp;本文主要介绍H5新增内容以及CSS3中的新特性。在H5方面主要介绍拓展了哪些内容，CSS3方面介绍动画及转换。
<!--more-->

#### H5新增内容

<font color=#9368d7 size=4 face="黑体">「1. 什么是HTML5」</font>

* 定义：HTML5定义了HTML标准的最新版本，是对HTML的第五次重大修改，号称下一代的HTML。
* 两个概念：
   * 是一个新版本的HTML语言，定义了新的标签、特性和属性
   * 拥有一个强大的技术集，这些技术集是指：HTML5、CSS3、JavaScript,这也是广义上的HTML5。

<font color=#9368d7 size=4 face="黑体">「2. HTML5拓展了哪些内容」</font>
* 语义化标签
* 本地存储
* 兼容特性
* 2D、3D
* 动画、过渡
* CSS3特性
* 性能与集成


<font color=#9368d7 size=4 face="黑体">「3. HTML5的现状」</font>

绝大多数新的属性，都已经被浏览器所支持，最新版本的浏览器已经开始陆续支持最新的特性，总的来说：HTML5已经是大势所趋。
 
##### HTML5新增标签

<font color=#9368d7 size=4 face="黑体">「1. 什么是语义化」</font>

语义化是指用HTML写出符合内容的结构化（内容语义化），选择合适的标签（代码语义化），能够便于开发者阅读和写出更优雅的代码的同时让浏览器的爬虫和机器很好地解析。

<font color=#9368d7 size=4 face="黑体">「2. 新增了哪些语义化标签」</font>

* header   ---  头部标签
* nav        ---  导航标签
* article ---   内容标签
* section ---   块级标签
* aside     ---   侧边栏标签
* footer   ---   尾部标签
![](https://i.loli.net/2021/04/04/VHwWxp4eR6c8AhY.png)
<font color=#9368d7 size=4 face="黑体">「3. 新增多媒体音频标签」</font>

* 多媒体标签有两个，分别是音频 audio和视频video。
* audio 标签说明
   * 可以在不使用标签的情况下，也能够原生的支持音频格式文件的播放，
   * 但是：播放的格式是有限的。
* audio支持的音频格式

 ![](https://i.loli.net/2021/04/04/pXgGo79aZfU52TB.png)
 * audio 的参数
 ![](https://i.loli.net/2021/04/04/PLIuW8ZpXCVyFY1.png)
 
```html

<audio controls>    
    <!-- 注意：在 chrome 浏览器中已经禁用了 autoplay 属性 -->   
    <!-- <audio src="./media/snow.mp3" controls autoplay></audio> -->   
    <!-- 
    因为不同浏览器支持不同的格式，所以我们采取的方案是这个音频准备多个文件 -->                               
<source src="myAudio.mp3" type="audio/mpeg"> 
<source src="myAudio.ogg" type="audio/ogg"> 
<p>Your browser doesn't support HTML5 audio. Here is   
a <a href="myAudio.mp4">link to the audio</a> instead.</p>
</audio>
```
<font color=#9368d7 size=4 face="黑体">「4. 新增多媒体视频标签」</font>

* video视频标签目前支持三种格式

![](https://i.loli.net/2021/04/04/S5EyIzpthreUa26.png)

* 语法格式
```html
<video src="./media/video.mp4" controls="controls"></video>
```
* video的参数

![](https://i.loli.net/2021/04/04/jdvMIfcGyh7BTHJ.png)

* video代码演示

```html
<body>  
     <!-- <video src="./media/video.mp4" controls="controls"></video> -->  
     <!-- 谷歌浏览器禁用了自动播放功能，如果想自动播放，需要添加 muted 属性 -->               <video controls="controls" autoplay muted loop poster="./media/pig.jpg">               <source src="./media/video.mp4" type="video/mp4">    
     <source src="./media/video.ogg" type="video/ogg"> 
     </video>
</body>
```