---
title: 初识canvas
date: 2021-06-02 14:52:16
tags: [canvas,]
categories: 可视化
---
教练！我想学canvas
>学习资料 MDN 掘金
<!-- more -->

# 概述
`<canvas>`用于在网页实时生成图像，并且可以操作图像内容，基本上它是一个可以用JavaScript操作的位图（bitmap）
`canvas`最早由Apple引入WebKit，用于Mac OS X的Dashboard，随后被各个浏览器实现。如今，所有的主流浏览器都支持它。
## 开始使用
* 一、首先新建一个`<canvas>` 网页元素。
~~~html
<canvas id="canvas" width="300px" height="300px">
        抱歉，您不配拥有canvas
</canvas>
~~~
上面代码中，如果浏览器不支持这个API，则会显示标签中间的文字——“抱歉，您不配有canvas”。
~~~css
canvas {
            border: 1px solid black;
            width: 200px;
            height: 300px;
        }
~~~
当没有设置宽度和高度的时候，canvas会初始化宽度为300像素和高度为150像素。
`canvas`标签中可以设置宽高，css中也可以设置宽高 如果两者不一致会发生什么呢？
上述代码中`canvas`标签中宽高比为1:1 css定义的宽高比为2:3
它可以使用CSS来定义大小，但在绘制时图像会伸缩以适应它的框架尺寸：如果CSS的尺寸与初始画布的比例不一致，它会出现扭曲。
![](https://i.loli.net/2021/06/03/rYPEHDX97eybjiK.png)
![](https://i.loli.net/2021/06/03/3BWnmPI25Hejctz.png)

* 每个**canvas**节点都有一个对应的**context**对象（上下文对象），canvas API定义在这个**context**，所欲需要获取这个对象，方法是使用**getContext**方法。
~~~js
const canvas = document.getElementById('canvas');
const ctx = canvas.getContext('2d');
~~~
上面代码中`getContext`方法指定参数`2d`表示该`canvas`节点用于生成2D图像（即平面图案）。如果参数是`webgl`，就表示生成3D图像（即立体图案），这部分实际上单独叫做WebGL API。
## 绘图方法
>`canvas`画布提供了一个用来作图的平面空间，该空间的每个点都有自己的坐标，x表示横坐标，y表示竖坐标。远点（0,0）位于图像左上角，x轴的正向是原点向右，y轴的正向是远点向下

### （1） 绘制路径
>`begnPath`表示开始绘制路径，`moveTo(x,y)`方法设置线段的起点，`lineTo(x,y)`方法设置线段的终点，`stroke`方法用来给透明的线段着色。

~~~js
ctx.beginPath() //开始路径绘制
ctx.moveTo(20,20) // 设置路径起点，坐标为（20,20）
ctx.lineTo(200,20) //绘制一条到（200,20）的直线
ctx.lineWidth = 1.0 // 设置线宽
ctx.strokeStyle = '#cc0000' //设置线的颜色
ctx.stroke() //进行线的着色，这时整条线才变得可见
~~~
![](https://i.loli.net/2021/06/03/SWC4B9N6OcUirkh.png)

moveTo和lineTo方法可以多次使用。最后，还可以使用closePath方法，自动绘制一条当前点到起点的直线，形成一个封闭图形，省去使用一次lineTo的方法。
### （2）绘制矩形

>`fillRect(x,y,width,height)`方法用来绘制矩形，它的四个参数分别为矩形左上角顶点的x坐标、y坐标以及矩形的宽和高。fillStyle属性用来设置矩形的填充色。

~~~js
ctx.fillStyle = "rgba(0, 0, 200, 0.5)";
ctx.fillRect(25, 25, 100, 100);
~~~

strokeRect方法和fillRect类似，用来绘制空心矩形。

~~~js
ctx.strokeRect(50, 50, 50, 50);
~~~
clearRect方法用来清楚某个矩形区域的内容。
~~~js
ctx.clearRect(45, 45, 60, 60);
~~~
下面是我绘制出的三个矩形
![](https://i.loli.net/2021/06/03/7n4IRtKNqSg1sHy.png)

### （3）绘制文本

>`fillText(string,x,y)`用来绘制文本，它的三个参数分别为文本内容、起点的x坐标、y坐标。使用之前，用font设置字体、大小、样式（写法类似于CSS的font属性）。与此类似的还有strokeText方法，用来添加空心字。

~~~js
// 设置字体
ctx.font = "bold 20px Arial"
// 设置对齐方式
ctx.textAlign = "left"
// 设置填充颜色
ctx.fillStyle = "#008600"
// 设置字体内容，以及在画布上的位置
ctx.fillText("Hello!", 10, 50);
// 绘制空心字
ctx.strokeText("Hello!",10,100)
~~~
>arc方法用来绘制扇形

~~~js
ctx.arc(x,y,redius,startAngle,endAngle,anticlockwise)
~~~

arc方法的x和y参数是圆心坐标，redius是半径，startAngle和endAngle则是扇形的起始角度和终止角度（以弧度表示），anticlockwise表示作图时应该逆时针画（true）还是顺时针画（false）。

下面是如何绘制实心的圆形。
~~~js
ctx.beginPath()
ctx.arc(60,60,50,0,Math.PI*2,true)
ctx.fillStyle = "#000000"
ctx.fill()
~~~

绘制空心圆形的例子。
~~~js
ctx.beginPath()
ctx.beginPath()
ctx.arc(60,60,50,0,Math.PI*2,true)
ctx.strokeStyle = "#000"
ctx.stroke()
~~~

### （5）设置渐变色

>createLinearGradient方法用来设置渐变色。

~~~js
var myGradient = ctx.createLinearGradient(0,0,0,160)
myGradient.addColorStop(0,"#BABABA")
myGradient.addColorStop(1,"#636363")
~~~
createLinearGradient方法参数是(x1,y1,x2,y2),其中x1和y1是起点坐标，x2和y2是终点坐标。通过不同的坐标值，可以设呢过程从上至下、从左到右的渐变等等。
使用方法如下
~~~js
ctx.fillStyle = myGradient
ctx.fillRect(10,10,200,100)
~~~

### （6）设置阴影

>一系列与阴影相关的方法，可以用来设置阴影。

~~~js
ctx.shadowOffsetX = 10 // 设置水平位移
ctx.shadowOffsetY = 10 // 设置垂直位移
ctx.shadowBlur = 5 // 设置模糊度
ctx.shadowColor = "rgba(0,0,0,0.5)" // 设置阴影颜色

ctx.fillStyle = "#c00"
ctx.fillRect(10,10,200,100)
~~~

## 图像处理方法

### drawImage方法
>Canvas API允许将图像文件插入画布，做法是读取图片后，使用`drawImage`方法在画布内进行重绘。
~~~js
const img = new Image()
img.src = 'image.png'
ctx.drawImage(img,0,0)  // 设置对应的图像对象，以及它在画布上的位置
~~~

上面代码将一个PNG图像载入画布。drawImage()方法接收三个参数，第一个参数是图像文件的DOM元素（即`<img>`节点），第二个和第三个参数是图像左上角在画布中的坐标，上列中的（0,0）就表示将图像左上角放置在画布左上角。

由于图像的载入需要时间，drawImage方法只能在图像完全载入后才能调用，因此上面的代码需要改写。

~~~js
const image = new Image()
image.addEventListener("load", function handle() {
        const canvas = document.createElement('canvas');
        canvas.width = image.width};
        canvas.height = image.height;
        canvas.getContext('2d').drawImage(image, 0, 0);
        // 插入页面底部
        document.body.appendChild(canvas);
        })
image.src = 'image.png'
~~~

### getImageData方法，putImageData方法
>getImageData方法可以用来读取canvas的内容，返回一个对象，包含了每个像素的信息。

~~~js

const imageData = ctx.getImageData(0,0,canvas.width,canvas.height)

~~~
这里遇到了一个图片加载到canvas上的跨域问题
https://developer.mozilla.org/en-US/docs/Web/HTML/CORS_enabled_image

imageData对象有一个data属性，它的值是一个一维数组。该数组的值，依次是每个像素的红、绿、蓝、alpha通道值，因此该数组的长度等于图像的像素宽度x图像像素高度x4，每个值得范围是0-255。这个数组不仅可读，而且可写，因此操作这个数组的值，就可以打到操作图像的目的。修改这个数组以后，使用putImageData方法将数组内容重新绘制在canvas上

~~~js
ctx.putImageData(imageData,0,0)
~~~

### toDataURL方法
>对canvas元素使用toDataURL方法可以将canvas图像转化成base64的数据格式
~~~js
function convertCanvasToImage(canvas) {
  var image = new Image();
  image.src = canvas.toDataURL('image/png');
  return image;
}
~~~

### save方法，restore方法

> save方法用于保存上下文环境，restore用于恢复到上次保存的上下文环境

~~~js
ctx.save();

ctx.shadowOffsetX = 10;
ctx.shadowOffsetY = 10;
ctx.shadowBlur = 5;
ctx.shadowColor = 'rgba(0,0,0,0.5)';

ctx.fillStyle = '#CC0000';
ctx.fillRect(10,10,150,100);

ctx.restore();

ctx.fillStyle = '#000000';
ctx.fillRect(180,10,150,100);
~~~

上面代码绘制了一个有阴影的矩形 然后用restore恢复到保存的状态绘制了一个没有阴影的矩形

