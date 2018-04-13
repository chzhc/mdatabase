[TOC]



# D3.js & Web

## 怎么使用 D3

学习 D3 最好的地方是： <http://d3js.org/> ，当然里面都是英文的，然后就是本站O(∩_∩)O~。

D3 是一个 JavaScript 函数库，并不需要通常所说的“安装”。它只有一个文件，在 HTML 中引用即可。有两种方法：

（1）下载 D3.js 的文件: [d3.zip](https://github.com/mbostock/d3/releases/download/v3.5.16/d3.zip)

解压后，在 HTML 文件中包含相关的 js 文件即可。

（2）还可以直接包含网络的链接，这种方法较简单：

```
<script src="http://d3js.org/d3.v3.min.js" charset="utf-8"></script>
```

但使用的时候要保持网络连接有效，不能再断网的情况下使用。

## 学习 D3 需要什么预备知识

想要通过 D3 来开启数据可视化之旅的朋友，需要什么预备知识呢？

- HTML：超文本标记语言，用于设定网页的内容
- CSS：层叠样式表，用于设定网页的样式
- JavaScript：一种直译式脚本语言，用于设定网页的行为
- DOM：文档对象模型，用于修改文档的内容和结构
- SVG：可缩放矢量图形，用于绘制可视化的图形

## 需要什么工具

制作网页常用的工具即可。

记事本软件：Notepad++、Editplus、Sublime Text 等，选择自己喜欢的即可。

浏览器：IE9 以上、Firefox、Chrome等，推荐用 Chrome

服务器软件：Apache、Tomcat 等

其中，服务器软件可能不是必须的，不过 D3 中有些函数需要将 HTML 文件放置于服务器目录下，才能正常使用，关于这点以后会再做说明。









``` html
<div id="chart"></div>
```



```js
d3.select('#chart')
  .selectAll("div")
  .data([4, 8, 15, 16, 23, 42])
  .enter()
  .append("div")
  .style("height", (d)=> d + "px")
```

所有不需要通过 JS 控制的视觉层内容都写到 CSS 中：

```
#chart div {
  display: inline-block;
  background: #4285F4;
  width: 20px;
  margin-right: 3px;
}
```

和柱状图不同地方在于，图表中根据数据变化的不再是元素的高度，而是元素的 background-color （背景色）。

```
const colorMap = d3.interpolateRgb(
  d3.rgb('#d6e685'),
  d3.rgb('#1e6823')
)

d3.select('#chart')
  .selectAll("div")
  .data([.2, .4, 0, 0, .13, .92])
  .enter()
  .append("div")
  .style("background-color", (d)=> {
    return d == 0 ? '#eee' : colorMap(d)
  })
```

colorMap 函数接收的输入值要在0到1之间，返回的是一个颜色值，这个值是在以输入值中两两数据为颜色值之间的渐变色值。插值法是图形编程和动画的关键点。在后面我们将会看到更多这方面的例子。







