# JavaScript

[TOC]

http://wiki.jikexueyuan.com/project/javascript/page-printing.html



# 位置结构

JavaScript 脚本可以很灵活的写在几乎 HTML 网页的任何地方。
但是，在 HTML 文件中编写的 JavaScript 脚本只可以放置在如下部分中：

1. HTML 网页的 `<head>...</head>` 里。
2. HTML 网页的 `<body>...</body>` 里。
3. HTML 网页的 `<head>...</head>` 和 `<body>...</body>` 里。
4. 外部文件里,并且引用在 `<head>...</head>` 中。

如下章节，我们将了解如何在上述 HTML 文件的不同的地方编写 JavaScript 脚本。

## 在 `<head>...</head>` 之间编写JavaScript 脚本

如果你希望在某个事件中编写脚本，比如，当用户点击一个按钮时触发一个事件。

你可以按照如下方式将脚本编写在 `<head>...</head>` 结构中

## 在 `<body>...</body>` 之间编写 JavaScript 脚本

如果你需要一段脚本来实现页面加载后将信息内容显示在页面中的功能。
这段脚本需要编写在HTML文件的 `<body>...</body>` 部分。

这种情况下，你不需要定义任何 JavaScript 函数。	

## script的位置

    <html>
    <head>
    <script type="text/javascript">
    <!--
    function sayHello() {
       alert("Hello World")
    }
    //-->
    </script>
    </head>
    <body>
    <input type="button" onclick="sayHello()" value="Say Hello" />
    </body>
    </html>
    <html>
    <head>
    </head>
    <body>
    <script type="text/javascript">
    <!--
    document.write("Hello World")
    //-->
    </script>
    <p>This is web page body </p>
    </body>
    </html>



## 在外部文件中编写 JavaScript 脚本

当你在工作中开始广泛使用 JavaScript 后，你会发现在多 HTML 页面中重用相同的 JavaScript 是一个不错的选择。

这样，你可以不用维护多个 HTML 文件中相同的代码。

Script 标签提供了在外部文件中编写 JavaScript 脚本并引用在 HTML 文件中的功能。

下面的例子将展示如何使用 script 标签将外部 JavaScript 脚本文件引用在 HTML 文件中。

```
    <html>
    <head>
    <script type="text/javascript" src="filename.js" ></script>
    </head>
    <body>
    .......
    </body>
    </html>
```

为了实现上述功能，需要将所有的 JavaScript 源代码编写到以 “.js” 为格式后缀名的外部文本文件中，然后按照上面的方式引用入 HTML 文件中。

例如，你可以将下面的内容编写到“文件名.js” 文件中，然后在 HTML 文件中引入该外部脚本文件后，你可以使用 sayHello 函数。

```
    function sayHello() {
       alert("Hello World")
    }
```

# 变量

## JavaScript 数据类型

可编程语言的基本特征之一就是对常用数据类型的支持。这些数据类型可以用可编程语言来表示和操作。

按照JavaScript的语法规范，它允许有如下三类基础数据类型：

1. 数值类型：比如 123,120.50 等。
2. 字符串类型：比如“This text string”。
3. 布尔类型：比如 true or false。

JavaScript也支持另外两个常用类型：null 和 undefined，这两个类型均仅限定一个单一的值。

除了上述的基础数据类型，JavaScript 也支持符合数据类型，我们称之为“对象”。我们会在其他章节中学习“对象”的具体内容。

**注意：**JAVA语言并区分整数类型与浮点类型。JavaScript 中的数值均使用浮点值来表示。同时，按照 IEEE754 标准，JavaScript 用64位浮点格式来表示数。

## JavaScript变量

和其他可编程语言相同，JavaScript 也有“变量”的概念。“变量”可以认为是有名字的容器。你可以将数据置于这些容器中，然后通过容器的名称就可以知道数据的类型。

值得注意的是，在 JavaScript 编程过程中，必须先声明一个变量，这个变量才能被使用。

此外，变量是通过 **“var”** 来声明的，例子如下：

```
    <script type="text/javascript">
    <!--
    var money;
    var name;
    //-->
    </script>
```

使用 **“var”** 关键词也可以同时声明多个变量。允许直接用等号初始化，例子如下：

```
    <script type="text/javascript">
    <!--
    var money, name;
    //-->
    </script>
```

## JavaScript变量作用域

一个变量的作用域就是该变量定义后在程序中的作用范围。JavaScript 变量有两个变量作用域。

1. 全局变量：全局变量具有全部整体范围的作用域，这意味着它可以在 JavaScript 代码任何地方定义。
2. 局部变量：局部变量仅在定义它的函数体内可以访问到。函数参数对于函数来说就是局部变量。

## JavaScript 变量名称

JavaScript 中变量的命名规则如下：

1. 不能使用 JavaScript 中的保留关键字来命名变量。这些保留关键字会在下一节中介绍。比如 break 或 boolean，这些命名变量是无效的。
2. JavaScript 变量名称不能以数字 （0-9） 开头，只能以字母或下划线来命名变量。比如 123tes t就是无效的变量名。但是， _123 就是有效的变量名。
3. JavaScript 变量名称对大小写敏感。比如，Name 和 name 是两个不同的变量。

# 运算符

基本与c++一致，增加`typeof()` 

# 条件选择

`if else`

- if statement
- if...else statement
- if...else if... statement.

`switch case` 与c++一致

```
    switch (expression)
    {
      case condition 1: statement(s)
    break;
      case condition 2: statement(s)
    break;
       ...
      case condition n: statement(s)
    break;
      default: statement(s)
    }  
```

# While 循环

循环语句就是在满足一定条件的情况下反复执行某一个操作。循环语句可以有效减少程序的行数。

*JavaScript* 支持所有必要的循环语句，以适用于编程过程中的所有情况。

## While 循环语句

**While**循环是 *JavaScript* 中最基本的循环模式，下边将加以介绍。

### 语法如下

```
    while(expression){  
        statement  
    }  
```

对于 **while** 循环，当条件表达式 *expression* 的返回值为真时，则执行“{}”中的语句，当执行完“{}”中的语句后，重新判断 *expression* 的返回值，知道表达式返回值的结果为假时，退出循环。

## do...While 循环语句

**do...while** 循环和 **while** 循环非常相似，它们之间的区别是 *while* 语句为先判断条件是否成立在执行循环体，而 *do...while* 循环语句则先执行一次循环后，再判断条件是否成立。也就是说即使判断条件不成立，*do...while* 循环语句中“{}”中的程序段至少要被执行一次。

```
    do{  
        statement  
    }while(expression);  
```

# For 循环

我们已经学习了几种不同的 **while** 循环，这一章我们来学习另一种更普及的循环 **for** 循环。

## for循环语句

**for** 循环是一种最简洁的循环模式，包括三个重要部分：

- *initialize* ：初始化表达式,初始化计数器一个初始值，在循环开始前计算初始状态。
- *test condition* ：判断条件表达式，判断给定的状态是否为真。如果条件为真，则执行循环体“{}”中的代码，否则跳出循环。
- *iteration statement* ：循环操作表达式，改变循环条件，修改计数器的值。

可以将这三个部分放在同一行，用分号隔开。

### 语法如下

```
    for(initialize;test condition;iteration statement)  
    {  
        statement;  
    }
```

# For...in

*JavaScript* 还支持另外一种循环模式，即 **for...in** 循环。这一种类型的循环将对象属性作为参数变量来实现循环。

对象的概念现在还没有讨论，对于 *for...in* 循环，用起来可能会觉得不舒服。但是一旦开始了解 *JavaScript* 中的对象概念，会发现 *for..in* 循环是非常有用的。

## 语法如下

```
    for (variablename in object){  
        statement
    }    
```

下面的例子用于打印出一个 *Web* 浏览器**导航**对象的属性：

```
    <script type="text/javascript">
    <!--
    var aProperty;
    document.write("Navigator Object Properties<br /> ");
    for (aProperty in navigator)
    {
        document.write(aProperty);
        document.write("<br />");
    }
    document.write("Exiting from the loop!");
    //-->
    </script>
```

# 循环控制

*JavaScript* 提供您完全控制来处理你的 *loops* 循环体和 *switch* 语句。可能会有这样一种情形，你需要在还没有到达循环体底部的时候跳出 *loop* 循环体。也可能存在这样的情况,当你想跳过一个代码块的一部分,想要开始下一个迭代。

为处理所有这些情况, *JavaScript* 提供了 *break* 和 *continue*语句。这些语句用于立即跳出来任何循环或开始下一个迭代循环。

## *Break* 语句

Break语句,简要介绍了switch语句,用于早期退出循环,打破封闭的花括号。

## *Continue* 语句

Continue语句告诉解释器立即开始下一次迭代的循环和跳过剩余的代码块。

当遇到continue语句,程序流将立即循环检查表达式,如果条件保持真那么下个迭代开始，否则控制跳出循环体。

## *使用标签来控制流

从 *JavaScript 1.2* ,开始，一个标签可以被用于 *break* , *continue* 语句去更精确地控制流。

一个标签仅仅是一个标识符后跟一个冒号,应用于声明或代码块。我们将看到两个不同的例子来理解标签 *break* 和 *continue* 。

注意: *continue* 或 *break* 语句及其标签的名字之间不允许有换行符。当然标签名称和其后的的循环体之间也不应当有任何其他语句。

```
    <script type="text/javascript">
    <!--
    document.write("Entering the loop!<br /> ");
    outerloop:   // This is the label name
    for (vari = 0; i< 5; i++)
    {
    document.write("Outerloop: " + i + "<br />");
    innerloop:
    for (var j = 0; j < 5; j++)
    {
    if (j >  3 ) break ; // Quit the innermost loop
    if (i == 2) break innerloop; // Do the same thing
    if (i == 4) break outerloop; // Quit the outer loop
    document.write("Innerloop: " + j + "  <br />");
       }
    }
    document.write("Exiting the loop!<br /> ");
    //-->
    </script>
```

# 函数

函数是一组可重用的代码，你可以在你程序的任何地方调用他。这使你不需要一遍又一遍地写相同的代码。这将帮助程序员编写模块代码。你可以把大项目在许多小和易于管理的功能。你可以把你的大型程序分成许多的小型的且易于管理的函数。

像任何其他高级编程语言，*JavaScript* 还支持使用函数编写模块代码所需的所有特性。

你一定在之前的章节见过 *alert()* 和 *write()* 函数。虽然我们将一次又一次的使用这些函数，但是他们已经被一次性的写在核心 *JavaScript* 。

*JavaScript* 使我们能够编写自己的函数。这一节将解释如何用 *JavaScript* 编写自己的函数。

## 函数定义

我们使用一个函数之前,我们需要定义该函数。在 *JavaScript* 中最常见的定义一个函数的方式是使用函数关键字，紧随其后的是一个独特的函数名，参数列表(也可能是空的)，和一个被花括号包围的语句块。这里显示的基本语法：

```
    <script type="text/javascript">
    <!--
    function functionname(parameter-list)
    {
        statements
    }
    //-->
    </script>
```

### 例子

让我们在sayhello函数上做一些修改。这次会有两个参数。

```
    <script type="text/javascript">
    <!--
    function sayHello(name, age)
    {
       alert( name + " is " + age + " years old.");
    }
    //-->
    </script>
```

**注意:** 我们使用+运算符连接字符串和数字。*JavaScript* 不介意将数字添加到字符串里。

现在，我们可以调用如下这个函数：

```
    <script type="text/javascript">
    <!--
    sayHello('Zara', 7 );
    //-->
    </script>
```

### reture 语句

一个 *JavaScript* 函数可以有一个可选的返回语句。这是必需的，如果你想从一个函数返回一个值。这个语句应该是函数里的最后一个语句。

例如你可以在一个函数内输入两个数字，然后你可以从函数返回调用他们相乘的结果。

# 事件

## 事件是什么？

*JavaScript* 与 *HTML* 的交互是通过事件来处理的，当用户或浏览器操纵页面时。事件就会发生。

当页面加载时,这是一个事件。当用户单击一个按钮，点击，是一个事件。另一个例子的事件就像按任何键，关闭窗口，调整窗口等。

开发人员可以使用这些事件来执行 *JavaScript* 编码的响应，如导致按钮关闭窗口，信息显示给用户，数据验证，和任何其他类型的反应的发生。

事件都是文档对象模型（*DOM*）三级的一部分，并且每个HTML元素有一定的事件可以触发 *JavaScript* 代码。

请通过这个小教程为更好地理解 *HTML* 事件参考。这里我们将看到一些例子来理解事件和 *JavaScript* 之间的关系：

## onclick 事件类型

这是最常用的事件类型，当用户点击鼠标左按钮。你可以把你的验证、警告等反对这个事件类型。

### 例子

```
    <html>
    <head>
    <script type="text/javascript">
    <!--
    function sayHello() {
       alert("Hello World")
    }
    //-->
    </script>
    </head>
    <body>
    <input type="button" onclick="sayHello()" value="Say Hello" />
    </body>
    </html>  
```

## onsubmit 事件类型

另一个最重要的是 *onsubmit* 事件类型。这个事件发生在您尝试提交一个表单。所以你可以用此事件类型进行表单验证。

下面一个简单简单的例子显示其用法。我们在这里调用 *validate()* 函数之前提交表单数据到网络服务器。如果 *validate()* 函数返回 *true* 表单将提交否则不会提交数据。

### 例子

```html
    <html>
    <head>
    <script type="text/javascript">
    <!--
    function validation() {
       all validation goes here
       .........
       return either true or false
    }
    //-->
    </script>
    </head>
    <body>
    <form method="POST" action="t.cgi" onsubmit="return validate()">
    .......
    <input type="submit" value="Submit" />
    </form>
    </body>
    </html>  
```

## HTML 4 标准事件

标准的 *HTML 4* 事件被列在这里,供您参考。执行以下脚本显示一个Javascript函数。

| Event       | Value  | Description                                            |
| ----------- | ------ | ------------------------------------------------------ |
| onchange    | script | Script runs when the element changes                   |
| onsubmit    | script | Script runs when the form is submitted                 |
| onreset     | script | Script runs when the form is reset                     |
| onselect    | script | Script runs when the element is selected               |
| onblur      | script | Script runs when the element loses focus               |
| onfocus     | script | Script runs when the element gets focus                |
| onkeydown   | script | Script runs when key is pressed                        |
| onkeypress  | script | Script runs when key is pressed and released           |
| onkeyup     | script | Script runs when key is released                       |
| onclick     | script | Script runs when a mouse click                         |
| ondblclick  | script | Script runs when a mouse double-click                  |
| onmousedown | script | Script runs when mouse button is pressed               |
| onmousemove | script | Script runs when mouse pointer moves                   |
| onmouseout  | script | Script runs when mouse pointer moves out of an element |
| onmouseover | script | Script runs when mouse pointer moves over an element   |
| onmouseup   | script | Script runs when mouse button is released              |

# Cookies

## Cookies 是什么？

Web 浏览器与服务器之间利用 HTTP 协议进行通信，而且 HTTP 是一种无状态的协议类型。但是对于商业网站，它通常需要维护一些与客户端在不同网页交互的信息。例如，一个用户在很多网页中填写信息后完成了注册，此时应该如何维护(或者缓存)众多网页中用户的信息。

在很多情况下，利用 Cookies 是最有效的方式，通过这种方式可以记录和跟踪一些用户的偏好，购物车，工资，和其他的信息，而利用这些信息可以更好的了解用户经历或者进行网站统计。

## 它是如何的起作用的？

服务器以 Cookies 的形式发送一些数据给访问者的浏览器。浏览器可以选择接收这些 Cookies。如果它被接收了，它就会被以纯文本的形式存储在访问者的硬盘中。接着，当访问者访问网站中的另外一个网页时，那些被缓存的 Cookies 会被发送到服务器进行检索。一旦服务器在服务端检索到该 Cookies 信息，服务器就会知道访问者本地已经缓存了的数据。

Cookies 是纯文本数据，记录了 5 个可变长度的字段：

- **Expires：** 这个字段记录 Cookies 有效时间长度。如果这个字段为空，该 Cookies 将会在用户关闭浏览器时过期，即该 Cookies 的数据不再可用。
- **Domain:** 这个字段记录网站名。
- **Path:** 这个字段记录设置 Cookies 的目录或者网页的路径。如果你想在任何目录或网页里面等够检索到 Cookies 数据这个字段可以被设置为空。
- **Secure:** 如果这个字段包含"secure"这个单词，那么 Cookies 仅仅只能被安全服务器进行检索。如果这个字段为空，就没有前面的限制。
- **Name=Value:** Cookies 以键值对的形式进行赋值和检索。

Cookies 最初是为 CGI 编程提供的，并且 Cookies 数据在网页浏览器和服务器之间是自动的传输的，因此服务器端的 CGI 脚本能够读写存储在客户端的 Cookies。

JavaScript 通过使用 Document 对象的 Cookies 属性同样可以操作 Cookies。JavaScript 可以读、创建、修改、和删除 Cookie，或者那些应用于当前网页的 Cookies。

## Cookies 的存储

创建 Cookie 最简单的方式就是给 document.cookie 对象赋值一个字符串值，它的语法如下：

### 语法

```html
document.cookie = "key1=value;key2=value2;expires=date";
```

这里的 expires 属性字段是可选的。如果你给它提供一个有效的日期或者时间值，Cookie 将会在你给定的日期或时间达到时过期，并且这之后 Cookie 的属性值都会变的不可访问。

**注意:** Cookie 的值不包括分号，逗号或者空格。因此，在存储 Cookie 之前，你可能需要利用 JavaScript 提供的*escape()* 函数来对其值进行转义。如果你按照那样做的话，当你读取 Cookie 的值时，你就需要利用相应的 *unescape()* 函数。

## 读取 Cookies

读取 Cookie 就像写它一样简单，因为 document.cookie 对象的值就是 Cookie 的属性值。因此你可以利用这个字符串在任何时候对 Cookie 进行访问。

document.cookie 字符串会保存一系列用分号分开的 name = value 键值对，这里的 name 就是一个 Cookie 名称，value 是它的值。

你可以利用 *split()* 函数将字符串分解成如下形式的 key 和 value：

### 例子

下面的是读取前面一节设置的 Cookie 信息。

```HTML
<html>
<head>
<script type="text/javascript">
<!--
function ReadCookie()
{
   var allcookies = document.cookie;
   alert("All Cookies : " + allcookies );

   // Get all the cookies pairs in an array
   cookiearray  = allcookies.split(';');

   // Now take key value pair out of this array
   for(var i=0; i<cookiearray.length; i++){
      name = cookiearray[i].split('=')[0];
      value = cookiearray[i].split('=')[1];
      alert("Key is : " + name + " and Value is : " + value);
   }
}
//-->
</script>
</head>
<body>
<form name="myform" action="">
<input type="button" value="Get Cookie" onclick="ReadCookie()"/>
</form>
</body>
</html>
```

**注意：**
这里的 length 是 Array 类型的一个方法，该方法返回一个数组的长度。我们会在一个单独的章节再讨论数组类型。在那个时候请再好好理解这个方法。

上面的代码会有如下的效果。当你按下 "Get Cookie" 按钮，你就会看到你在上一节设置的 Cookie 信息。

**注意：**
在你的机器上可能已经存在一些其他的 Cookie。因此上面的代码会显示存储在你的机器上所有的 Cookies 信息。

## 设置 Cookies 有效日期

你可以设置有效日期并且将该有效日期与 Cookie 进行绑定，从而可以延长 Cookie 的生存时间超过当前浏览器的会话。这个可以为 expires 属性赋值一个日期或者时间来实现。

### 例子

下面的代码说明怎样设置让 Cookie 在一个月之后失效：

```HTML
<html>
<head>
<script type="text/javascript">
<!--
function WriteCookie()
{
   var now = new Date();
   now.setMonth( now.getMonth() + 1 ); 
   cookievalue = escape(document.myform.customer.value) + ";"
   document.cookie="name=" + cookievalue;
   document.cookie = "expires=" + now.toUTCString() + ";"
   alert("Setting Cookies : " + "name=" + cookievalue );
}
//-->
</script>
</head>
<body>
<form name="formname" action="">
Enter name: <input type="text" name="customer"/>
<input type="button" value="Set Cookie" onclick="WriteCookie()"/>
</form>
</body>
</html>
```

# 页面重定向

## 什么是页面重定向？

当你点击一个 URL 会跳转到页面 X，但是在一个页面内部点击会直接跳转到另外一个页面 Y，这里能够跳转的原因是因为页面重定向。这个是与 JavaScript 页面刷新是有区别的。

这里有许多原因可以解释为什么想要从原始页面进行重定向。例举了如下几个原因：

- 你不喜欢你现在的域名，并且你想要使用一个新的域名。有时你想将你的所有的访问者转向到你的新的网站。在这种情况下，你可以继续维护你旧的域名，同时增加单独的一页用来进行重定向，这样你的所有旧域名的访问者就可以转到新的域名。
- 你已经基于浏览器的版本构建了各种网页或者他们的名称在不同的国家不同，你可以客户端网页让用户重定向到合适的网页，而不是在服务器端进行网页的跳转。
- 搜索引擎可能已经对你的网页建立了索引。但是当你网站迁移到另外一个域名时，你不想丢失通过搜索引擎访问你网站的用户。此时你可以使用客户端网页重定向。但是请记住，不要利用这种方式欺骗搜索引擎，否则你的网站会被禁止访问。

## 网页重定向如何工作的？

### 例子 1

利用 JavaScript 在客户端进行重定向是非常简单的。为了重定向你网站的访问者，你仅仅只需要在网页代码的头部中添加一行代码，如下：

```
<head>
<script type="text/javascript">
<!--
   window.location="http://www.newlocation.com";
//-->
</script>
</head>
```

### 例子 3

下面是一个基于用户的浏览器跳重定向网页到不同的网页的例子：

```
<head>
<script type="text/javascript">
<!--
var browsername=navigator.appName; 
if( browsername == "Netscape" )
{ 
   window.location="http://www.location.com/ns.htm";
}
else if ( browsername =="Microsoft Internet Explorer")
{
   window.location="http://www.location.com/ie.htm";
}
else
{
  window.location="http://www.location.com/other.htm";
}
//-->
</script>
</head>
```

# 对话框

JavaScript 支持三种重要的对话框类型。这些对话框可以用来弹出警告，或者根据用户的输入来得到确定的信息，或者得到用户输入的某一类型。

## 警告对话框

警告对话框是最常用的，它通常被用来给用户提示一些警告信息。比如，某个输入区域需要用户输入一些文本信息，但是用户并没有输入任何信息，那么为了使用户输入有效的信息，你可以利用警告对话框来提示警告信息，如下：

```
<head>
<script type="text/javascript">
<!--
   alert("Warning Message");
//-->
</script>
</head>
```

除了这个作用外，警告对话框也可以提示一些友好的信息。警告对话框仅仅值提供一个 "OK" 按钮供选择来继续执行。

## 确认对话框

确认对话框是最常用来获取用户对任何选项的赞成的观点。确认对话框会显示两个按钮：**Ok** 和 **Cancel**。

如果用户点击了 OK 按钮，窗口函数 *confirm()* 的返回值为 true。如果用户点击了 Cancel 按钮，*confirm()* 函数返回值为 false。你可以像如下的方式使用确认对话框：

```
<head>
<script type="text/javascript">
<!--
   var retVal = confirm("Do you want to continue ?");
   if( retVal == true ){
      alert("User wants to continue!");
      return true;
   }else{
      alert("User does not want to continue!");
      return false;
   }
//-->
</script>
</head>
```

## 提示对话框

当你想弹出一个文本框，并且得到用户的输入数据，提示框就可以实现这个功能。因此，这个框可以与用户进行交互。用户需要填写信息，然后点击 Ok 按钮。

这种对话框通过调用 *prompt()* 函数来显示，给函数有两个形式参数 (i) 你想在框中显示的文本标签 (ii) 一个默认的显示在框中的字符串。

这种对话框提供两个按钮：**OK** 和 **Cancel**。 如果用户点击 OK 按钮，窗口函数 *prompt()* 将会返回文本框中输入的值。如果用户点击 Cancel 按钮，窗口函数 *prompt()* 的返回值为 *null*。

你可以使用如下的方式来实现提示对话框：

```
<head>
<script type="text/javascript">
<!--
   var retVal = prompt("Enter your name : ", "your name here");
   alert("You have entered : " +  retVal );
//-->
</script>
</head>
```

# Void 关键字

JavaScript 中 **void** 是一个重要的关键字。它可以用作一个一元运算符，此时它会出现在一个操作数之前，这个操作数可以是任意类型的。

这个操作符指定要计算一个表达式但是不返回值。它的语法可能是下列之一:

```
    <head>
    <script type="text/javascript">
    <!--
    void func()
    javascript:void func()

    or:

    void(func())
    javascript:void(func())
    //-->
    </script>
    </head> 
```

## 例子 1

这个运算符最常用在客户端的 *javascript: URL* 中，在 URL 中可以写带有副作用的表达式，而 void 则让浏览器不必显示这个表达式的计算结果。

这里的 *alert('Warning!!!')* 表达式被执行了，但是它不会在当前文档处装入任何内容：

```
    <head>
    <script type="text/javascript">
    <!--
    //-->
    </script>
    </head>
    <body>
    <a href="javascript:void(alert('Warning!!!'))">Click me!</a>
    </body>
```

## 例子 2

另外的一个例子，下面的超级链接并不会做任何事情，因为在 JavaScript 中表达式 “0” 没有任何作用。这里的表达式 “0” 已被计算，但是它并没有在当前文档处装入任何内容：

```
    <head>
    <script type="text/javascript">
    <!--
    //-->
    </script>
    </head>
    <body>
    <a href="javascript:void(0))">Click me!</a>
    </body>
```

## 例子 3

*void* 的另一种用法是有意的生成 *undefined* 值，如下所示：

```
    <head>
    <script type="text/javascript">
    <!--
    function getValue(){
       var a,b,c;

       a = void ( b = 5, c = 7 );
       document.write('a = ' + a + ' b = ' + b +' c = ' + c );
    }
    //-->
    </script>
    </head>
```

# 页面打印

很多时候你会想在你 web 页面上添加一个按钮来用实际的打印机打印当前页面的内容。

JavaScript 能使用 *window* 对象的打印函数来帮你实现这个功能。

当JavaScript的打印方法 **window.print()** 执行后，就会打印当前的 web 页面。

你可以使用 *onclick* 事件直接调用这个函数，如下所示：

```
    <head>
    <script type="text/javascript">
    <!--
    //-->
    </script>
    </head>
    <body>
    <form>
    <input type="button" value="Print" onclick="window.print()" />
    </form>
    </body>
```

这段代码会产生如下的所示的按钮，它能让你打印当前的页面。试着点击一下：

```
<head>
<script type="text/javascript">
<!--
//-->
</script>
</head>
<body>
<form>
<input type="button" value="Print" onclick="window.print()" />
</form>
</body>
```

虽然这能够满足你将页面打印出来的要求，但并不推荐这种方法将页面提供给打印设备。一个友好的打印页面，仅仅是打印一个包含文本的页面，而不包括图片，图形或者广告。

你可以采用以下方法之一来使一个页面友好的打印：

- 拷贝一份页面，并且删去不想要的文本和图形，然后从原始页面链接到能友好打印的页面。
- 如果你不想额外的拷贝一份页面，那你可以使用合适的注释来标记可以打印的文本。例如，`<!-PRINT STARTS HERE -->..... <!-PRINT ENDS HERE -->`。然后你可以使用 PERL 或者其他任何语言的脚本在后台对最终可打印的文本进行净化并展示出来。我们网站使用的是同样的方法来提供给网站访问者的打印设备打印。

## 怎样打印一个页面

如果某个页面并没有提供如上所示的打印工具，那你可以使用浏览器的标准工具栏来打印出 web 页面。操作流程如下所示：

文件-->打印-->点击确定按钮。



# ***对象**

# 对象概述

JavaScript 是一种面向对象编程语言 ( OOP ) 。一种语言如果可以为开发者提供四种基本功能就可以被称为面向对象:

- **封装**：把相关信息，无论数据或方法都存储在一个对象中的能力。
- **聚合**：将一个对象存储在另一个对象中的能力。
- **继承**：一个类依赖另一个类 （ 或一些类 ）中的一些性质和方法的能力。
- **多态**：写一个函数或方法，这个函数或方法可以以各种不同形式工作的能力。

对象由属性组成。如果一个属性包含一个函数，它被认为是这个对象的一个方法，否则这个属性被认为成一个属性。

## 对象的属性

对象属性可以是任何三个基本数据类型，或抽象数据类型中的任何一个，如另一个对象。对象属性通常是在对象的方法内部使用的变量，也可以是在整个页面中使用的全局变量。

将属性添加到对象的语法是：

```
objectName.objectProperty = propertyValue； 
```

### 示例

以下是一个简单的例子，介绍了如何使用文档对象的 “title” 属性来获得一个文档标题:

```
var str = document.title;
```

## 对象的方法

方法是函数让对象做某事或让某事作用在这个对象上。一个函数和一个方法之间，除了函数是声明的一个独立的单元，而方法是附加到某个对象上并且可以使用 `this` 关键字引用方法，除此之外几乎没有差别。

方法从将对象的内容显示到屏幕上到对一组本地属性和参数执行复杂的数学运算都很有用。

### 示例

以下是一个简单的例子，介绍了怎样使用文档对象的 **write()** 方法在文档中写任何内容:

```
document.write("This is test");
```

## 用户定义的对象

所有用户定义的对象和对象中的内置对象都是一个被称为 Object 的对象的后代。

### new 运算符

new 运算符用于创建对象的实例。若要创建一个对象，new 运算符后紧接着是构造函数方法。

在以下示例中，构造函数方法是 `Object()` ， `Array()` ，和 `Date()` 。这些函数是内置的 JavaScript 函数。

```
var employee = new Object();  
var books = new Array("C++","Perl","Java");  
var day = new Date("August 15,1947");   
```

### Object() 构造函数

构造函数是一个可以创建和初始化对象的函数。 JavaScript 提供了名为`Object()` 的一个特殊的构造函数来生成对象。 `Object()` 构造函数将返回值赋给一个变量。

### 示例 1

此示例演示如何创建一个对象：

```html
    <html>
    <head>
    <title>User-defined objects</title>
    <script type="text/javascript">
    var book = new Object();   // Create the object
        book.subject = "Perl"; // Assign properties to the object
        book.author  = "Mohtashim";
    </script>
    </head>
    <body>
    <script type="text/javascript">
       document.write("Book name is : " + book.subject + "<br>");
       document.write("Book author is : " + book.author + "<br>");
    </script>
    </body>
    </html>
```

### 示例 2

此示例演示了如何用用户定义的函数来创建一个对象。这里的 this 关键字用于引用被传递给函数的对象。

```HTML
    <html>
    <head>
    <title>User-defined objects</title>
    <script type="text/javascript">
    function book(title, author){
        this.title = title; 
        this.author  = author;
    }
    </script>
    </head>
    <body>
    <script type="text/javascript">
       var myBook = new book("Perl", "Mohtashim");
       document.write("Book title is : " + myBook.title + "<br>");
       document.write("Book author is : " + myBook.author + "<br>");
    </script>
    </body>
    </html>
```

## 为对象定义的方法

前面的示例演示了如何用构造函数创建对象和分配属性。但是我们需要通过给对象分配方法来完成一个对象的定义。

前面的示例演示了如何用构造函数创建对象和分配属性。但是我们需要通过给对象分配方法来完成一个对象的定义。

### 示例

这里是一个简单的示例，说明了如何与一个对象一起添加一个函数。

```HTML
    <html>
    <head>
    <title>User-defined objects</title>
    <script type="text/javascript">

    // Define a function which will work as a method
    function addPrice(amount){
        this.price = amount; 
    }

    function book(title, author){
        this.title = title; 
        this.author  = author;
        this.addPrice = addPrice; // Assign that method as property.
    }

    </script>
    </head>
    <body>
    <script type="text/javascript">
       var myBook = new book("Perl", "Mohtashim");
       myBook.addPrice(100);
       document.write("Book title is : " + myBook.title + "<br>");
       document.write("Book author is : " + myBook.author + "<br>");
       document.write("Book price is : " + myBook.price + "<br>");
    </script>
    </body>
    </html>
```

## with 关键字

with 关键字被用来作为用于引用一个对象的属性或方法的一种速记。

对象被指定成 with 关键字的参数，进而成为后面语句块的默认对象。这个对象的下的属性和方法可以不指定对象名而直接使用。

### 语法

```javascript
     with (object){
    properties used without the object name and dot
    }    
```

### 示例

```html
    <html>
    <head>
    <title>User-defined objects</title>
    <script type="text/javascript">

    // Define a function which will work as a method
    function addPrice(amount){
    with(this){
       price = amount; 
    }
    }
    function book(title, author){
        this.title = title; 
        this.author  = author;
        this.price = 0;
        this.addPrice = addPrice; // Assign that method as property.
    }
    </script>
    </head>
    <body>
    <script type="text/javascript">
       var myBook = new book("Perl", "Mohtashim");
       myBook.addPrice(100);
       document.write("Book title is : " + myBook.title + "<br>");
       document.write("Book author is : " + myBook.author + "<br>");
       document.write("Book price is : " + myBook.price + "<br>");
    </script>
    </body>
    </html>    
```

## JavaScript Native 对象

JavaScript 有几个内置或 native 对象。这些对象可以在任何地方被访问，并且在任何浏览器中运行的任何操作系统的工作方式相同。

这里是所有重要的 JavaScript native 对象的列表：

- [JavaScript Number Object](http://wiki.jikexueyuan.com/project/javascript/number.html)
- [JavaScript Boolean Object](http://wiki.jikexueyuan.com/project/javascript/boolean.html)
- [JavaScript String Object](http://wiki.jikexueyuan.com/project/javascript/string.html)
- [JavaScript Array Object](http://wiki.jikexueyuan.com/project/javascript/array.html)
- [JavaScript Date Object](http://wiki.jikexueyuan.com/project/javascript/date.html)
- [JavaScript Math Object](http://wiki.jikexueyuan.com/project/javascript/math.html)
- [JavaScript RegExp Object](http://wiki.jikexueyuan.com/project/javascript/regexp.html)








以前总以为JS是按照代码的顺序依次执行，直到进入工作后，遇到各种JS问题，才直到以前的知识是多么的浅薄，最近，看了很多有关JS执行顺序的技术博客，总结一下。
一、几个概念如下
1、代码块
由<script>标签分割的代码区域称作一个代码段，如下代码，有两个代码段。

```
<script type="text/javascript">
      //代码块一
</script>
<script type="text/javascript">
       //代码块二
</script>
```

2、变量
3、函数
JS当中，函数分为声明式函数和赋值式函数，声明式函数与赋值式函数的区别在于：在JS的预编译期，声明式函数将会先被提取出来，然后才按顺序执行js代码。

```
function declaration(){ 
             //这是声明式函数
     }
         
var assignment= function{  
             //这是赋值式函数
     }
```

4、编译期与执行期
JS的解析过程分为两个阶段编译期与执行期。
编译期是JS对本代码块中的所有声明的变量和声明式函数进行处理，此时处理函数的只是声明式函数，而且变量也只是进行了声明但未进行初始化以及赋值。

二、Javascript的执行顺序

![img](//upload-images.jianshu.io/upload_images/3403199-e3eb5d4b8084d15a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

JS执行顺序机制.jpg

1、JS的执行顺序是按照代码块的顺序来执行的，代码块间相互独立，但变量和方法共享。
因为代码块间的独立性，下面的代码中第一个代码块运行报错也不影响代码块二的执行，而代码块二中能调用到代码一中的变量，这是因为变量具有块间共享性。

```
<script type="text/javascript">
      alert(str);   //因为没有定义str，所以浏览器会出错，改代码块下面的代码将不能能运行
      alert("first block");    // 没有运行到这里
      var varone= "variable in block one";
</script>
<script type="text/javascript">
      alert("second block");      //可以被执行到
      alert(varone);    //可以执行，弹出variable in block one
</script>
```

2、几个demo（转至其他博客）

```
alert(testNum);  
alert('ok');  
testNum = 2;  
//testNum未声明，执行报错。
```

```
alert(testNum);  
alert('ok');  
var testNum = 2;  
//弹出undefined和ok。预编译的时候，声明了变量testNum ；执行到alert(testNum )这行代码的时候，
testNum 尚未被赋值，所以弹出undefined。  


var testNum = 2; 
alert(testNum);  
alert('ok');  
//弹出2和ok。预编译的时候，声明了变量testNum ，
  并且testNum 赋值=2在alert(testNum)前面，所以弹出2和ok;  
```

```
testFunc();  
function testFunc() {}  
alert('ok');  
//弹出ok。预编译的时候，解析了定义式函数语句function testFunc() {}，顺利执行。  
```

```
testFunc();  
var testFunc = function() {};  
alert('ok');  
//testFunc不是函数，执行报错。预编译的时候，声明了变量testFunc = undefined；执行到testFunc()时，testFunc还等于undefined，不是函数，所以执行testFunc()会报错。  
```

```
<script type="text/javascript">  
    testFunc();  
    alert('first block');  
</script>  
<script type="text/javascript">  
    alert('second block');  
</script>  
//弹出second block。因为JS是一段一段执行的，第一段执行到testFunc()的时候报错，整个第一段都不会再执行，第二段正常执行。 
```

```
<script type="text/javascript">
     Fn();  //执行结果："执行了函数2",同名函数后者会覆盖前者
     function Fn(){ //函数1
        alert("执行了函数1");
     }
         
     function Fn(){  //函数2
        alert("执行了函数2");
     }
</script>  
```

```
<script type="text/javascript">
      Fn();  //执行结果："执行了声明式函数",在预编译期声明函数及被处理了，所以即使Fn()调用函数放在声明函数前也能执行。
      function Fn(){ //声明式函数
         alert("执行了声明式函数");
      }
         
      var Fn = function(){  //赋值式函数
         alert("执行了赋值式函数");
      }
</script>
```

```
//代码块一
<script type="text/javascript">
      alert(str);//浏览器报错,但并没有弹出信息窗
</script>
//代码块二
<script type="text/javascript">
      alert(str); //弹窗"undefined"
      var str = "aaa";
</script>
//js在预处理期对变量进行了声明处理,但是并没有进行初始化与赋值，所以导致代码块二中的变量是unfiened的，而代码一中的变量是完全不存在的，所以浏览器报错。
<script type="text/javascript">
      Fn();  //浏览器报错:"undefined"
</script>
<script type="text/javascript">
      function Fn(){ //函数1
          alert("执行了函数1");
      }
</script>
```






























































































































