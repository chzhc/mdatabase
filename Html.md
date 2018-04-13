# Html

1. **<meta charset="utf-8">** 



```
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>菜鸟教程(runoob.com)</title>
</head>
<body>
    <h1>我的第一个标题</h1>
    <p>我的第一个段落。</p>
</body>
</html>
```



- **<!DOCTYPE html>** 声明为 HTML5 文档
- **<html>** 元素是 HTML 页面的根元素
- **<head>** 元素包含了文档的元（meta）数据
- **<title>** 元素描述了文档的标题
- **<body>** 元素包含了可见的页面内容
- **<h1>** 元素定义一个大标题
- **<p>** 元素定义一个段落
- ​

## 什么是HTML?

HTML 是用来描述网页的一种语言。

- HTML 指的是超文本标记语言: **H**yper**T**ext **M**arkup **L**anguage
- HTML 不是一种编程语言，而是一种**标记**语言
- 标记语言是一套**标记标签** (markup tag)
- HTML 使用标记标签来**描述**网页
- HTML 文档包含了HTML **标签**及**文本**内容
- HTML文档也叫做 **web 页面**

## HTML 标签

HTML 标记标签通常被称为 HTML 标签 (HTML tag)。

- HTML 标签是由*尖括号*包围的关键词，比如 <html>
- HTML 标签通常是*成对出现*的，比如 <b> 和 </b>
- 标签对中的第一个标签是*开始标签*，第二个标签是*结束标签*
- 开始和结束标签也被称为*开放标签*和*闭合标签*

> <标签>内容</标签>



## HTML 元素

"HTML 标签" 和 "HTML 元素" 通常都是描述同样的意思.

但是严格来讲, 一个 HTML 元素包含了开始标签与结束标签，如下实例:

HTML 元素:

<p>这是一个段落。</p>



## HTML 网页结构

下面是一个可视化的HTML页面结构：

![1522912628742](C:\Users\ZHANGC~1\AppData\Local\Temp\1522912628742.png)



## <!DOCTYPE> 声明

<!DOCTYPE>声明有助于浏览器中正确显示网页。

网络上有很多不同的文件，如果能够正确声明HTML的版本，浏览器就能正确显示网页内容。

doctype 声明是不区分大小写的，以下方式均可：

<!DOCTYPE html> 
<!DOCTYPE HTML> 
<!doctype html> 
<!Doctype Html>

### HTML5

<!DOCTYPE html>

### HTML 4.01

```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
"http://www.w3.org/TR/html4/loose.dtd">
```

### XHTML 1.0

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
"http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
```

查看完整网页声明类型 [DOCTYPE 参考手册](http://www.runoob.com/tags/tag-doctype.html)。



## 中文编码

目前在大部分浏览器中，直接输出中文会出现中文乱码的情况，这时候我们就需要在头部将字符声明为 UTF-8。

```
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>页面标题</title>
</head>
<body>
 
<h1>我的第一个标题</h1>
 
<p>我的第一个段落。</p>
 
</body>
</html>
```



## HTML 标题

HTML 标题（Heading）是通过<h1> - <h6> 标签来定义的.

## HTML 段落

HTML 段落是通过标签 <p> 来定义的.

## HTML 链接

HTML 链接是通过标签 <a> 来定义的.

```href
<a href="http://www.runoob.com">这是一个链接</a>
```

## HTML 图像

HTML 图像是通过标签 <img> 来定义的.



## HTML 元素语法

- HTML 元素以**开始标签**起始
- HTML 元素以**结束标签**终止
- **元素的内容**是开始标签与结束标签之间的内容
- 某些 HTML 元素具有**空内容（empty content）**
- 空元素**在开始标签中进行关闭**（以开始标签的结束而结束）
- 大多数 HTML 元素可拥有**属性**

## 不要忘记结束标签

即使您忘记了使用结束标签，大多数浏览器也会正确地显示 HTML：

```
<p>这是一个段落
<p>这是一个段落
```

以上实例在浏览器中也能正常显示，因为关闭标签是可选的。

但不要依赖这种做法。忘记使用结束标签会产生不可预料的结果或错误。

## HTML 空元素

没有内容的 HTML 元素被称为空元素。空元素是在开始标签中关闭的。

```
没有内容的 HTML 元素被称为空元素。空元素是在开始标签中关闭的。
<br> 就是没有关闭标签的空元素（<br> 标签定义换行）。
在 XHTML、XML 以及未来版本的 HTML 中，所有元素都必须被关闭。
在开始标签中添加斜杠，比如 <br />，是关闭空元素的正确方法，HTML、XHTML 和 XML 都接受这种方式。
即使 <br> 在所有浏览器中都是有效的，但使用 <br /> 其实是更长远的保障。
```

## HTML 提示：使用小写标签

**==HTML 标签对大小写不敏感==**：<P> 等同于 <p>。许多网站都使用大写的 HTML 标签。

菜鸟教程使用的是小写标签，因为万维网联盟（W3C）在 HTML 4 中**推荐**使用小写，==而在未来 (X)HTML 版本中**强制**使用小写。==



## HTML 属性

- HTML 元素可以设置**属性**
- 属性可以在元素中添加**附加信息**
- 属性一般描述于**开始标签**
- 属性总是以名称/值对的形式出现，**比如：name="value"**。

### 属性实例

HTML 链接由 <a> 标签定义。链接的地址在 **href 属性**中指定：

```
<a href="http://www.runoob.com">这是一个链接</a>
```

### HTML 属性常用引用属性值

属性值应该始终被包括在引号内。

双引号是最常用的，不过使用单引号也没有问题。

**提示:** 在某些个别的情况下，比如属性值本身就含有双引号，那么您必须使用单引号

例如：name='John "ShotGun" Nelson'

==与python的引号一致==

### HTML 提示：使用小写属性*

属性和属性值对大小写不敏感。

不过，万维网联盟在其 HTML 4 推荐标准中推荐小写的属性/属性值。

而新版本的 (X)HTML 要求使用小写属性。

### HTML 属性参考手册

查看完整的HTML属性列表: [HTML 标签参考手册](http://www.runoob.com/tags/html-reference.html)。

下面列出了适用于大多数 HTML 元素的属性：

| 属性  | 描述                                                         |
| ----- | ------------------------------------------------------------ |
| class | 为html元素定义一个或多个类名（classname）(类名从样式文件引入) |
| id    | 定义元素的唯一id                                             |
| style | 规定元素的行内样式（inline style）                           |
| title | 描述了元素的额外信息 (作为工具条使用)                        |

## HTML 水平线

<hr> 标签在 HTML 页面中创建水平线。
hr 元素可用于分隔内容。

```
<p>这是一个段落。</p>
<hr>
<p>这是一个段落。</p>
<hr>
<p>这是一个段落。</p>


<hr>无所谓匹配
```

==不参加匹配，一个hr对应一个横线==



## HTML 注释

可以将注释插入 HTML 代码中，这样可以提高其可读性，使代码更易被人理解。浏览器会忽略注释，也不会显示它们。

注释写法如下:

> <!-- 这是一个注释 -->

## HTML 段落

段落是通过 <p> 标签定义的。

```
<p>这是一个段落
<p>这是另一个段落
```

## HTML 折行

如果您希望在不产生一个新段落的情况下进行换行（新行），请使用 `<br />` 标签：

```
<p>这个<br>段落<br>演示了分行的效果</p>
```

## HTML 输出- 使用提醒

我们无法确定 HTML 被显示的确切效果。屏幕的大小，以及对窗口的调整都可能导致不同的结果。

对于 HTML，您无法通过在 HTML 代码中添加额外的空格或换行来改变输出的效果。

当显示页面时，浏览器会移除源代码中多余的空格和空行。所有连续的空格或空行都会被算作一个空格。需要注意的是，HTML 代码中的所有连续的空行（换行）也被显示为一个空格。

==html不判定除自定标志如`<br>`等以外的空格以及换行符==

## HTML 文本格式化

HTML 使用标签 <b>("bold") 与 <i>("italic") 对输出的文本进行格式, 如：**粗体** or *斜体*

这些HTML标签被称为格式化标签（请查看底部完整标签参考手册）。

```
通常标签 <strong> 替换加粗标签 <b> 来使用, <em> 替换 <i>标签使用。

然而，这些标签的含义是不同的：

<b> 与<i> 定义粗体或斜体文本。

<strong> 或者 <em>意味着你要呈现的文本是重要的，所以要突出显示。现今所有主要浏览器都能渲染各种效果的字体。不过，未来浏览器可能会支持更好的渲染效果。
```

## HTML 文本格式化标签

| 标签                                                  | 描述               |
| ----------------------------------------------------- | ------------------ |
| [b](http://www.runoob.com/tags/tag-b.html)            | 定义粗体文本       |
| [em](http://www.runoob.com/tags/tag-em.html)          | 定义着重文字       |
| [i](http://www.runoob.com/tags/tag-i.html)            | 定义斜体字         |
| [small](http://www.runoob.com/tags/tag-small.html)    | 定义小号字         |
| [strong](http://www.runoob.com/tags/tag-strong.html)  | 定义加重语气       |
| [sup](http://www.runoob.com/tags/tag-sub.html)        | 定义下标字         |
| [sub](http://www.runoob.com/html/m/tags/tag-sup.html) | 定义上标字         |
| [ins](http://www.runoob.com/tags/tag-ins.html)        | 定义插入字(下划线) |
| [del](http://www.runoob.com/tags/tag-del.html)        | 定义删除字         |

## HTML "计算机输出" 标签

| 标签                                             | 描述               |
| ------------------------------------------------ | ------------------ |
| [code](http://www.runoob.com/tags/tag-code.html) | 定义计算机代码     |
| [kbd](http://www.runoob.com/tags/tag-kbd.html)   | 定义键盘码         |
| [samp](http://www.runoob.com/tags/tag-samp.html) | 定义计算机代码样本 |
| [var](http://www.runoob.com/tags/tag-var.html)   | 定义变量           |
| [pre](http://www.runoob.com/tags/tag-pre.html)   | 定义预格式文本     |

## HTML 引文, 引用, 及标签定义

| 标签                                                         | 描述               |
| ------------------------------------------------------------ | ------------------ |
| [abbr](http://www.runoob.com/tags/tag-abbr.html)             | 定义缩写           |
| [address](http://www.runoob.com/tags/tag-address.html)       | 定义地址           |
| [bdo](http://www.runoob.com/tags/tag-bdo.html)               | 定义文字方向       |
| [blockquote](http://www.runoob.com/tags/tag-blockquote.html) | 定义长的引用       |
| [q](http://www.runoob.com/tags/tag-q.html)                   | 定义短的引用语     |
| [cite](http://www.runoob.com/tags/tag-cite.html)             | 定义引用、引证     |
| [dfn](http://www.runoob.com/tags/tag-dfn.html)               | 定义一个定义项目。 |









































































