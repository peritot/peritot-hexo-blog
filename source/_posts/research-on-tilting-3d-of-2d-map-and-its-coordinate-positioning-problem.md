---
title: 二维地图的倾斜三维化及其坐标定位问题的研究
date: 2018-09-18 12:50:50
tags: JavaScript
categories: work
---

## 背景

公司内部的地图框架不支持地图的三维自由倾斜旋转，无法实现如下图的类似效果。要实现这种效果，框架源码级别的修改工作量巨大。如何能用较为简单方法实现这种三维自由倾斜旋转的效果，是一个值得研究的问题。

![](./1.jpg)

既然整个地图框架的三维实现较为复杂，能不能先将地图转化为图片，对图片进行三维化？答案是能。当然地图图片的三维化需要解决两个问题：

* 如何倾斜旋转三维化图片
* 如何将地理要素准确定位到三维化的地图图片上

下面我们逐个解决。

## 图片倾斜三维化

要实现图片的倾斜旋转有多种方式，这里我们选择利用 CSS3 的 transform 属性来实现。这种方法实现起来较为简单，同时跨浏览器的兼容性也较好。查询 [Can I use](https://caniuse.com/#search=transform) 网站可以看到， CSS3 3D Transforms 属性各个浏览器的支持情况都很好， IE 下只有 transform-style属性不支持。这个属性我们用不到，不影响本文的功能实现。（IE10 同样测试通过。）

### 3D Transforms

如何理解 CSS3 的 3D Transforms 呢？

其实这与数学意义上的 3D 是相通的，我们都知道，数学意义上 3D 实际是用三个相互垂直的坐标系 X、 Y、 Z 来描述的。同样的，网页上的 3D 也是用 X、 Y、 Z 三轴来描述，只是方向与通常的数学上的三轴不太一致。如图。

![](./3.png)

通过查阅 [W3Schools](https://www.w3schools.com/cssref/css3_pr_transform.asp) 我们知道 transform有很多属性，如 `matrix`， `translate`， `scale`， `rotate`，`skew`， `perspective`，实际上本文只需要用到 `rotate` （包括 `rotateX`，`rotateY`， `rotateZ`）和 `scale` 。

* **rotate**

`rotate` 旋转， `rotateX` 旋转 X 轴， `rotateY` 旋转 Y 轴， `rotateZ` 旋转 Z 轴。

![](./3.1.png)

* **scale**

`scale` 缩放， `scale(x, y)` ，x 、y 表示相应轴方向上的缩放倍数。

![](./3.2.png)

### 图片倾斜旋转

理解了上述的两个属性，我们就可以分析如何对图片进行倾斜旋转了。

以东城区为例，要将图片倾斜旋转成为如下图的效果：

![](./4.png)

要经过几个步骤：

1. 将图片按照 Z 轴方向旋转，如 `rotateZ(45deg)` 。

![](./5.png)

2. 将图片按照 X 轴方向旋转，表现出来为倾斜效果，如 `rotateX(60deg)` 。

![](./6.png)

3. 计算倾斜旋转后的图片大小与原图像大小的比例，对图像进行等比例缩放到合适尺寸，如 `scale(0.5)` 。此步骤的目的是保证倾斜旋转后的图片不超过地图范围。

![](./7.png)

## 地理要素定位

地图图片的倾斜旋转完成后，我们就得到了三维化的地图图片。后面要着手研究地理要素坐标的坐标转换与定位问题，这其实是本文的难点，无法完成坐标的转换，上面的所有努力将付诸东流。

一个完整的坐标转换的思路应该是：

* 将地理坐标转换为屏幕坐标
* 屏幕坐标的坐标系移动到以图片中点为原点的坐标系
* 对中点坐标系进行 X 轴、 Y 轴、 Z 轴的旋转及缩放操作
* 将以图片中点为原点的坐标系移动回默认的以图片左上点为原点的坐标系

要实现上述步骤，首先需要知道地图图片的如下参数，（以东城区为例）：

* 地图的地理坐标范围：`extentX: 501079.66053476278, extentY: 312697.36648548726`
* 屏幕每一像素对应的地理长度：`extentRatio: 23.203159431643467`

具体实现细节如下：

1. 地理坐标转屏幕坐标
``` JavaScript
function coordToScreen (x, y) {
    var xs = (x - extentX) / extentRatio;
    var ys = (extentY - y) / extentRatio;

    return [xs, ys];
}
```

2. 移动坐标系到中点
``` JavaScript
// sw，sh 分别表示图片的宽度和高度 
function screenToOrigin (xs, ys, sw, sh) {
    var xo = xs - sw / 2;
    var yo = ys - sh / 2;

    return [xo, yo];
}
```

3. 旋转缩放
``` JavaScript
function originTransform (xo, yo, rx, ry, rz, rate) {
    rx = rx / 180 * Math.PI;
    ry = ry / 180 * Math.PI;
    rz = rz / 180 * Math.PI;

    // z
    var xz = xo * Math.cos(rz) - yo * Math.sin(rz);
    var yz = xo * Math.sin(rz) + yo * Math.cos(rz);

    // x
    var xx = xz * rate;
    var yx = yz * Math.cos(rx);

    // y
    var xy = xx * Math.cos(ry);
    var yy = yx * rate;

    return [xy, yy];
}
```

4. 移回坐标系
``` JavaScript
function originToScreen (xo, yo, sw, sh) {
    var xs = xo + sw / 2;
    var ys = yo + sh / 2;

    return [xs, ys];
}
```

得到地理要素转换后的屏幕坐标后，就可以在相应坐标上绘制图标，从而实现坐标的定位。效果如下：

![](./8.png)

## 总结

本文研究了二维地图的倾斜三维化方法及其地理要素坐标转换定位的问题，最终地图图片的三维化效果较好，能满足一般的三维地图显示需求。

![](./9.jpg)
