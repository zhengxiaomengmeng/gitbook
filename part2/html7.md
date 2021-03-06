# canvas

**HTML5 的标准已经出来好久了，但是似乎其中的 Canvas 现在并没有在太多的地方用到。**

**一个很重要的原因是，Canvas的标准还没有完全确定，不适合大规模用在生产环境。**

**但是，Canvas 的优点也是很明显的，例如在绘制含有大量元素的图表的时候，SVG 往往因为性能问题而无法胜任，例如我见过的一次技术分享会的抽奖环节，虽然效果比较炫，但因为每个头像都是 DOM，利用 CSS3 控制的动画，导致了性能非常低下。**

**此外，随着硬件性能的提高，视频截图、图像处理等功能也逐渐可以在网页上实现了，大多数网站用的是 Flash，但是 Flash 在 Mac 电脑上性能不高，还需要学一些额外的知识。**

**Canvas 则是直接使用 JavaScript 来进行绘图，对 Mac 友好，所以不失为 Flash 的一个继承者。**

## svg是个啥？

**SVG 指的是可伸缩矢量图形 (Scalable Vector Graphics),它用来定义用于网络的基于矢量的图形，使用 XML 格式定义图形。**

**SVG 图像在放大或改变尺寸的情况下其图形质量不会有所损失。**

**此外SVG 是万维网联盟的标准，SVG 与诸如 DOM 和 XSL 之类的 W3C 标准是一个整体。**

[HTML5学习--SVG全攻略（基础篇）](https://www.jianshu.com/p/2ea0ec6800d7)

## 使用 Canvas

说了这么多，Canvas 究竟是个啥？

英文中 Canvas 的意思是“画布”，不过这里说的 Canvas 是 HTML5 中新出的一个元素，开发者可以在上面绘制一系列图形。Canvas 在 HTML 文件中的写法很简单：

```
<canvas id="canvas" width="宽度" height="高度"></canvas>
```
**其中 id 属性是所有 HTML 元素都可以用的，Canvas 自带的属性只有后面两个（分别控制宽度、高度），没有其它的了。至于兼容性，CanIUse 上面写了，基础的功能目前用户使用的 90% 的浏览器都支持，所以大部分情况下还是可以放心使用的。**

**注意，一定要使用 Canvas 自带的 width 和 height 属性，不要使用 CSS 来控制，因为 CSS 控制会导致 Canvas 变形。**

**绝大部分的绘图方法都与 <canvas> 标签无关，需要使用 JavaScript 对其进行操作，这就是所谓的 Canvas API。**

**我们首先获取到这个元素：**


```
var canvas = document.getElementById('canvas');
```
**然后通过一个方法来获取可以调用一切 Canvas API 的入口：**

```
var ctx = canvas.getContext('2d');
```
看到 2d 是不是很激动地联想到有没有 3d 呢？没有 3d 的写法，不过如果想要开启 3D 世界的大门，则可以写 canvas.getContext('webgl')。然而 WebGL 是基于 OpenGL ES 2.0 的一套标准，与本文是彻彻底底的两条路，因此这里就不讨论了

## Canvas 中的基本概念

### 坐标

与数学上常见的笛卡尔坐标系不太相同，Canvas 的坐标系是计算机中常见的坐标系，它长这样：

![image](https://dn-rexskz.qbox.me/blog/article/canvas/0.png)

画布的最左上角是 (0,0)，往右 x 增大，往下 y 增大，而且 x 和 y 都是整数（就算在计算过程中不是整数，在绘制的时候也会当作整数处理），单位是像素。

### 绘图

带大家怀旧一下。不知道有多少同学小时候玩过 logo 语言，在里面你可以控制一只小海龟在一块板子上行走、画画、提笔、落笔。Canvas 中也一样，你需要控制一只画笔的移动和绘制。然而 Canvas 更高级一些，你可以直接利用一些函数来画图，不用去控制那只画笔的位置。

### Canvas 中的基本图形

通过上文定义的 ctx 变量可以干许多有意思的事情，我们先看看如何绘制一些基本图形。

#### 线条

我们指定画笔移动到某一点，然后告诉画笔需要从当前这一点画到另一点。我们可以让画笔多次移动、绘制，最后统一输出到屏幕上。例子如下：

```
ctx.moveTo(10, 10);
ctx.lineTo(150, 50);
ctx.lineTo(10, 50);
ctx.moveTo(10, 20);
ctx.lineTo(40, 70);
ctx.stroke();
```
上面的代码中，lineTo 是产生线条用的函数，执行完之后画笔就移到了线条的终点。需要注意的是，线条此时并没有显示在屏幕上，必须调用 stroke 才会显示。这样设计是有道理的，因为向屏幕上输出内容需要耗费大量的资源，我们完全可以先攒够一波 lineTo，最后用 stroke 放一个大的。

#### 路径

绘制路径非常简单，只需要先告诉 ctx 一声“我要开始画路径了”，然后通过各种方法（例如 lineTo）绘制路径。如果需要画一个封闭路径，那就最后告诉 ctx一声：“我画完了，你把它封闭起来吧。”当然，不要忘记利用 stroke 输出到屏幕上。

一个简单的例子：

```
ctx.beginPath();
ctx.moveTo(10, 10);
ctx.lineTo(150, 50);
ctx.lineTo(10, 50);
ctx.closePath();
ctx.stroke();
```
如果我不想只描绘路径线条，而是想填充整个路径呢？可以将最后一行的 stroke 改成 fill，这样就跟使用了画图中的油漆桶一样，封闭路径里面的内容就都被填充上颜色了：

```
ctx.fill();
```
#### 弧 / 圆形

绘制弧的函数参数比较多：

```
ctx.arc(圆心 x 坐标, 圆心 y 坐标, 半径, 起始角度, 终止角度, 是否为逆时针);
```
注意，在 Canvas 的坐标系中，角的一边是以圆心为中心的水平向右的直线。角度单位均为弧度。例如下图，确定了圆心、起始角度（图中标明的锐角）和终止角度（图中标明的钝 角），方向为逆时针，于是就有了这么一个弧。如果方向为顺时针，那么就会是一个跟它互补的、非常非常大的弧……

![image](https://dn-rexskz.qbox.me/blog/article/canvas/3.png)

所以如果转了 2π 圈之后，弧就成了圆形，因此也可以使用绘制弧的方式来绘制圆形：

```
ctx.beginPath();
ctx.arc(圆心 x 坐标, 圆心 y 坐标, 半径, 0, Math.PI * 2, true);
ctx.closePath();
```
最后一个参数随便填（当然也可以不填），因为不管是顺时针还是逆时针，转了 2π 圈之后都是一个圆。

[大牛的博客](http://www.techug.com/post/html5-canvas.html)