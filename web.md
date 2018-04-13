---
typora-copy-images-to: ..\Pictures\Typora
---

# Web容器

[TOC]

​	web容器是一种服务程序，在服务器一个端口就有一个提供相应服务的程序，而这个程序就是处理从客户端发出的请求，如JAVA中的Tomcat容器，ASP的IIS或PWS都是这样的容器。一个服务器可以有多个容器。

## tomcat

​	Tomcat 很受广大程序员的喜欢，因为它运行时占用的系统资源小，扩展性好，支持[负载平衡](https://baike.baidu.com/item/%E8%B4%9F%E8%BD%BD%E5%B9%B3%E8%A1%A1)与邮件服务等开发应用系统常用的功能；而且它还在不断的改进和完善中，任何一个感兴趣的程序员都可以更改它或在其中加入新的功能。

​	Tomcat 是一个小型的轻量级[应用服务器](https://baike.baidu.com/item/%E5%BA%94%E7%94%A8%E6%9C%8D%E5%8A%A1%E5%99%A8)，普遍用于中小型系统和并发访问量较低的场合，是开发和调试JSP 程序的首选。对于一个初学者来说，可以这样认为，当在一台机器上配置好Apache 服务器，可利用它响应对HTML 页面的访问请求。实际上Tomcat 部分是Apache 服务器的扩展，但它是独立运行的，所以当你运行tomcat 时，它实际上作为一个与Apache 独立的进程单独运行的。

​	这里的诀窍是，当配置正确时，Apache 为HTML页面服务，而Tomcat 实际上运行JSP 页面和Servlet。另外，Tomcat和IIS、Apache等Web服务器一样，具有处理HTML页面的功能，另外它还是一个Servlet和JSP容器，独立的Servlet容器是Tomcat的默认模式。不过，Tomcat处理静态HTML的能力不如Apache服务器。

### Tomcat 和 Apache的区别

​	[Apache](https://link.zhihu.com/?target=http%3A//httpd.apache.org/ABOUT_APACHE.html) 和 [Tomcat](https://link.zhihu.com/?target=http%3A//tomcat.apache.org/) 都是web网络服务器，两者既有联系又有区别，在进行HTML、PHP、JSP、Perl等开发过程中，需要准确掌握其各自特点，选择最佳的服务器配置。

　　Apache是web服务器（静态解析，如HTML），tomcat是java应用服务器（动态解析，如JSP）

　　Tomcat只是一个servlet(jsp也翻译成servlet)容器，可以认为是apache的扩展，但是可以独立于apache运行













# Servlet 教程

Servlet 为创建基于 web 的应用程序提供了基于组件、独立于平台的方法，可以不受 CGI 程序的性能限制。Servlet 有权限访问所有的 Java API，包括访问企业级数据库的 JDBC API。

本教程将讲解如何使用 Java Servlet 来开发基于 web 的应用程序。

[菜鸟Servlet教程](http://www.runoob.com/servlet/servlet-tutorial.html)



​	Servlet 是在服务器上运行的小程序。这个词是在 Java applet的环境中创造的，Java applet 是一种当作单独文件跟网页一起发送的小程序，它通常用于在客户端运行，结果得到为用户进行运算或者根据用户互作用定位图形等服务。
	服务器上需要一些程序，常常是根据用户输入访问数据库的程序。这些通常是使用公共网关接口（Common Gateway Interface，CGI）应用程序完成的。然而，在服务器上运行 Java，这种程序可使用 Java 编程语言实现。在通信量大的服务器上，JavaServlet 的优点在于它们的执行速度更快于 CGI 程序。各个用户请求被激活成单个程序中的一个线程，而无需创建单独的进程，这意味着服务器端处理请求的系统开销将明显降低。

**实现过程**
最早支持 Servlet 技术的是 JavaSoft 的 Java Web Server。此后，一些其它的基于 Java 的 Web Server 开始支持标准的 Servlet API。Servlet 的主要功能在于交互式地浏览和修改数据，生成动态 Web 内容。这个过程为：

1. 客户端发送请求至服务器端；
2. 服务器将请求信息发送至 Servlet；
3. Servlet 生成响应内容并将其传给服务器。响应内容动态生成，通常取决于客户端的请求；
4. 服务器将响应返回给客户端。



​	Servlet 看起来像是通常的 Java 程序。Servlet 导入特定的属于 Java Servlet API 的包。因为是对象字节码，可动态地从网络加载，可以说 Servlet 对 Server 就如同 Applet对 Client 一样，但是，由于 Servlet 运行于 Server 中，它们并不需要一个图形用户界面。从这个角度讲，Servlet 也被称为 FacelessObject。

​	一个 Servlet 就是 Java 编程语言中的一个类，它被用来扩展服务器的性能，服务器上驻留着可以通过“请求-响应”编程模型来访问的应用程序。虽然 Servlet 可以对任何类型的请求产生响应，但通常只用来扩展 Web 服务器的应用程序。

## Servlet 是什么？

Java Servlet 是运行在 Web 服务器或应用服务器上的程序，它是作为来自 Web 浏览器或其他 HTTP 客户端的请求和 HTTP 服务器上的数据库或应用程序之间的中间层。

使用 Servlet，您可以收集来自网页表单的用户输入，呈现来自数据库或者其他源的记录，还可以动态创建网页。

Java Servlet 通常情况下与使用 CGI（Common Gateway Interface，公共网关接口）实现的程序可以达到异曲同工的效果。但是相比于 CGI，Servlet 有以下几点优势：

- 性能明显更好。
- Servlet 在 Web 服务器的地址空间内执行。这样它就没有必要再创建一个单独的进程来处理每个客户端请求。
- Servlet 是独立于平台的，因为它们是用 Java 编写的。
- 服务器上的 Java 安全管理器执行了一系列限制，以保护服务器计算机上的资源。因此，Servlet 是可信的。
- Java 类库的全部功能对 Servlet 来说都是可用的。它可以通过 sockets 和 RMI 机制与 applets、数据库或其他软件进行交互。

## Servlet 架构

下图显示了 Servlet 在 Web 应用程序中的位置。

![Servlet 架构](http://www.runoob.com/wp-content/uploads/2014/07/servlet-arch.jpg)

## Servlet 任务

Servlet 执行以下主要任务：

- 读取客户端（浏览器）发送的显式的数据。这包括网页上的 HTML 表单，或者也可以是来自 applet 或自定义的 HTTP 客户端程序的表单。
- 读取客户端（浏览器）发送的隐式的 HTTP 请求数据。这包括 cookies、媒体类型和浏览器能理解的压缩格式等等。
- 处理数据并生成结果。这个过程可能需要访问数据库，执行 RMI 或 CORBA 调用，调用 Web 服务，或者直接计算得出对应的响应。
- 发送显式的数据（即文档）到客户端（浏览器）。该文档的格式可以是多种多样的，包括文本文件（HTML 或 XML）、二进制文件（GIF 图像）、Excel 等。
- 发送隐式的 HTTP 响应到客户端（浏览器）。这包括告诉浏览器或其他客户端被返回的文档类型（例如 HTML），设置 cookies 和缓存参数，以及其他类似的任务。

## Servlet 包

Java Servlet 是运行在带有支持 Java Servlet 规范的解释器的 web 服务器上的 Java 类。

Servlet 可以使用 **javax.servlet** 和 **javax.servlet.http** 包创建，它是 Java 企业版的标准组成部分，Java 企业版是支持大型开发项目的 Java 类库的扩展版本。

这些类实现 Java Servlet 和 JSP 规范。在写本教程的时候，二者相应的版本分别是 Java Servlet 2.5 和 JSP 2.1。

Java Servlet 就像任何其他的 Java 类一样已经被创建和编译。在您安装 Servlet 包并把它们添加到您的计算机上的 Classpath 类路径中之后，您就可以通过 JDK 的 Java 编译器或任何其他编译器来编译 Servlet。





# Java Applet 

​	Applet是采用Java编程语言编写的小应用程序，该程序可以包含在 HTML（标准通用标记语言的一个应用）页中，与在页中包含图像的方式大致相同。 
	含有Applet的网页的HTML文件代码中部带有`<applet> 和</applet>`这样一对标记，当支持Java的网络浏览器遇到这对标记时，就将下载相应的小应用程序代码并在本地计算机上执行该Applet。

## 工作原理

​	Java Applet 可以大大提高Web页面的交互能力和动态执行能力。包含Applet的网页被称为Java-powered页，可以称其为Java支持的网页。

![img](C:\Users\ZhangConghai\Pictures\Typora\f636afc379310a554a1dea87b74543a982261083.jpg)

​	 当用户访问这样的网页时，Applet被下载到用户的计算机上执行，但前提是用户使用的是支持Java的网络浏览器。由于Applet是在用户的计算机上执行的，所以它的执行速度不受网络带宽或者Modem存取速度的限制，用户可以更好地欣赏网页上Applet产生的多媒体效果。

​	在Java Applet中，可以实现图形绘制，字体和颜色控制，动画和声音的插入，人机交互及网络交流等功能。 Applet还提供了名为抽象窗口工具箱（Abstract Window Toolkit， AWT）的窗口环境开发工具。 AWT利用用户计算机的GUI元素，可以建立标准的图形用户界面，如窗口、按钮、滚动条等等。目前，在网络上有非常多的Applet范例来生动地展现这些功能，读者可以去调阅相应的网页以观看它们的效果。

​	Applet 小应用程序的实现主要依靠java.applet包中的Applet类。与一般的应用程序不同，Applet应用程序必须嵌入在HTML页面中，才能得到解释执行；同时Applet可以从Web页面中获得参数，并和Web页面进行交互。
Applet是一种Java的小程序，它通过使用该Applet的HTML文件，由支持Java的网页浏览器下载运行。也可以通过java开发工具的appletviewer来运行。Applet 程序离不开使用它的HTML文件。这个HTML文件中关于Applet的信息至少应包含以下三点：

1. 字节码文件名(编译后的Java文件，以.class为后缀)
2. 字节码文件的地址
3. 在网页上显示Applet的方式。

一个HTML文件增加Applet有关的内容只是使网页更加富有生气，如添加声音、动画等这些吸引人的特征，它并不会改变HTML文件中与Applet无关的元素。

## 安全性限制

为了防止这样的问题出现，在缺省的情况下，大多数浏览器在Applet安全方面受到诸多的限制，几乎不能对系统进行任何“读”或“写”的操作：

1. 不允许Applet访问本地文件系统
2. Applet不能执行任何本地计算机上的程序
3. 不允许Applet尝试打开除提供Applet的服务器之外的任何系统的Socket
4. 不允许Applet访问用户名、电子邮件地址等与本地系统有关的信息
5. 对于Applet而言，Java环境中的某些标准系统属性是只读的
6. Applet不能创建或装载Applet的机器的网络连接
7. Applet不能充当网络服务器，监听或接收来自远程系统的连接请求
8. 不允许Applet装载动态库或定义本地方法调用
9. Applet不能操纵不在自己线程组中的任何线程
10. Applet不能关闭JVM。





### 开发步骤

Applet程序开发主要步骤如下：

1)选用EDIT或Windows Notepad等工具作为编辑器建立Java Applet源程序。

2)把Applet的源程序转换为字节码文件。

3)编制使用class 的HTML文件。在HTML文件内放入必要的`<OBJECT>`语句。

下面举一个最简单的HelloWorld 例子来说明Applet程序的开发过程：

**(1) 编辑Applet 的java源文件**

> 创建文件夹C:\ghq，在该文件夹下建立 HelloWorld.java

**(2)编译Applet**

编译HelloWorld.java源文件可使用如下JDK命令：

> C:\ghq\>javac HelloWorld.java<Enter>

**(3)创建HTML文件**

在运行创建的HelloWorld.class 之前，还需创建一个HTML文件，[appletviewer](https://baike.baidu.com/item/appletviewer)或浏览器将通过该文件访问创建的Applet。为运行HelloWorld.class, 需要创建包含如下HTML语句的名为HelloWorld.html的文件。

```
<HTML>
<TITLE>HelloWorld! Applet</TITLE>
<APPLET
CODE="HelloWorld.class" //这里应该是HelloWorld.class
WIDTH=200
HEIGHT=100>
</APPLET>
</HTML>
```

**(4)执行 HelloWorld.html**

如果用[appletviewer](https://baike.baidu.com/item/appletviewer)运行HelloWorld.html,需输入如下的命令行：

> C:\ghq\>appletviewer HelloWorld.html<ENTER>

​	可以看出，该命令启动了appletviewer并指明了HTML文件，该HTML文件中包含对应于HelloWorld 的`<APPLET>`语句。
如果用浏览器运行HelloWorld Applet，需在浏览器的地址栏中输入HTML文件URL地址。
至此，一个Applet程序的开发运行整个过程结束了(包括java源文件、编译的class文件、html文件以及用appletviewer或用浏览器运行)。

## 常用方法

applet的[超类](https://baike.baidu.com/item/%E8%B6%85%E7%B1%BB)是panel,

public final void setStub([AppletStub](https://baike.baidu.com/item/AppletStub) stub)

//设置Applet的stub.stub是Java和C之间转换参数并返回值的代码位，它是由系统自动设定的。

public boolean isActive();// 判断一个Applet是否处于活动状态。

public URL getDocumentBase();// 检索表示该Applet运行的[文件目录](https://baike.baidu.com/item/%E6%96%87%E4%BB%B6%E7%9B%AE%E5%BD%95)的对象。

public URL getCodeBase();// 获取该Applet 代码的URL地址。

public String getParameter(String name)；// 获取该Applet 由name指定参数的值。

public [AppletContext](https://baike.baidu.com/item/AppletContext) getAppletContext()；// 返回浏览器或小应用程序观察器。

public void resize(int width,int height)；// 调整Applet运行的窗口尺寸。

public void resize(Dimension d)；// 调整Applet运行的窗口尺寸。

public void showStatus(String msg)；// 在浏览器的状态条中显示指定的信息。

public Image getImage(URL url)； // 按url指定的地址装入图象。

public Image getImage(URL url,String name)；// 按url指定的地址和文件名加载图像。

public [AudioClip](https://baike.baidu.com/item/AudioClip) getAudioClip(URL url)；// 按url指定的地址获取声音文件。

public AudioClip getAudioClip(URL url, String name)；// 按url指定的地址和文件名获取声音。

public String getAppletInfo()；// 返回Applet应用有关的作者、版本和版权方面的信息；

public String[][] getParameterInfo()；

// 返回描述Applet参数的字符串[数组](https://baike.baidu.com/item/%E6%95%B0%E7%BB%84)，该数组通常包含三个字符串： 参数名、该参数所需值的类型和该参数的说明。

public void play(URL url)；// 加载并播放一个url指定的音频剪辑。

public void destroy()；//撤消Applet及其所占用的资源。若该Applet是活动的，则先终止该Applet的运行。

### 运行状态

Applet类中的四种基本方法用来控制其运行状态：init()、start()、stop()、destroy()

**init()方法**

这个方法主要是为Applet的正常运行做一些初始化工作。当一个Applet被[系统调用](https://baike.baidu.com/item/%E7%B3%BB%E7%BB%9F%E8%B0%83%E7%94%A8)时，系统首先调用的就是该方法。通常可以在该方法中完成从网页向Applet传递参数，添加用户界面的基本组件等操作。

**start()方法**

系统在调用完init()方法之后，将自动调用start()方法。而且，每当用户离开包含该Applet的主页后又再返回时，系统又会再执行一遍start()方法。这就意味着start()方法可以被多次执行，而不像init()方法。因此，可把只希望执行一遍的代码放在init()方法中。可以在start()方法中开始一个线程，如继续一个动画、声音等。

**stop()方法**

这个方法在用户离开Applet所在页面时执行，因此，它也是可以被多次执行的。它使你可以在用户并不注意Applet的时候，停止一些耗用系统资源的工作以免影响系统的运行速度，且并不需要人为地去调用该方法。如果Applet中不包含动画、声音等程序，通常也不必实现该方法。

**destroy()方法**

与对象的finalize()方法不同，Java在浏览器关闭的时候才调用该方法。Applet是嵌在HTML文件中的，所以destroty()方法不关心何时Applet被关闭，它在浏览器关闭的时候自动执行。在destroy()方法中一般可以要求收回占用的非内存独立资源。(如果在Applet仍在运行时浏览器被关闭，系统将先执行stop()方法，再执行destroy()方法。

### 有关参数说明

利用Applet来接收从HTML中传递过来的参数,下面对这些参数作一简单说明：

*** CODE标志**

CODE标志指定Applet的类名；WIDTH和HEIGHT标志指定Applet窗口的像素尺寸。在APPLET语句里还可使用其他一些标志。

*** CODEBASE 标志**

CODEBASE标志指定Applet的URL地址。Applet的通用资源定位地址URL，它可以是[绝对地址](https://baike.baidu.com/item/%E7%BB%9D%E5%AF%B9%E5%9C%B0%E5%9D%80) 。也可以是相对于当前HTML所在目录的[相对地址](https://baike.baidu.com/item/%E7%9B%B8%E5%AF%B9%E5%9C%B0%E5%9D%80)，如/AppletPath/Name。如果HTML文件不指定CODEBASE 标志，浏览器将使用和HTML文件相同的URL。

*** ALT 标志**

虽然Java在WWW上很受欢迎，但并非所有浏览器都对其提供支持。如果某浏览器无法运行Java Applet，那么它在遇到APPLET语句时将显示ALT标志指定的文本信息。

*** ALIGN 标志**

ALIGN标志可用来控制把Applet窗口显示在HTML文档窗口的什么位置。与HTML<LMG>语句一样，ALIGN标志指定的值可以是TOP、MIDDLE或BOTTOM。

*** VSPACE与HSPACE 标志**

VSPACE和HSPACE标志指定浏览器显示在Applet窗口周围的水平和竖直空白条的尺寸，单位为像素。

*** NAME 标志**

NAME标志把指定的名字赋予Applet的当前实例。当浏览器同时运行两个或多个Applet时，各Applet可通过名字相互引用或交换信息。如果忽略NAME标志，Applet的名字将对应于其类名。

*** PARAM 标志**

通用性是程序设计所追求的目标之一。使用户或者程序员能很方便地使用同一个Applet完成不同的任务是通用性的具体表现。从HTML文件获取信息是提高Applet通用性的一条有效途径。

假设编制了一个把某公司的名字在屏幕上卷动的Applet。为了使该Applet更加通用，则可以使该Applet从HTML文件获取需要卷动的文本信息。这样，若想显示另一个公司的名字，用不着修改Java Applet本身，只需修改HTML文件即可。

PARAM 标志可用来在HTML文件里指定参数，格式如下所示：

PARAM Name="name" Value="Liter"

Java Applet可调用getParameter方法获取HTML文件里设置的参数值。

上面这个例子就是一个简单主页的HTML文件代码。代码第五行中的<P>，是为了确保Applet出现在新的一行，若没有它， Applet将会紧接着上一行的最后一个单词出现。代码第六、七两行是关于Applet的一些参数。其中第六行是必需的Applet参数，定义了编译后的包含Applet字节码的文件名，后缀通常为“.class”；和以象素为单位的Applet的初始宽度与高度。第七行则是附加的Applet参数，它由一个分离的标记来指定其后的名称和值，在这里是img的值为“example.gif’，它代表了一个图形文件名。

Applet的下载与图形文件一样需要一定的时间，若干秒后它才能在屏幕上显示出来。等待的时间则取决于Applet的大小和用户的网络连接的速度。一旦下载以后，它便和本地计算机上的程序以相同的速度运行了。

Applet在用户的计算机上执行时，还可以下载其它的资源，如声音文件、图像文件或更多的Java代码，有些Applet还允许用户进行交互式操作。但这需要重复的链接与下载，因此速度很慢，这是一个亟待解决的问题，可以想到的一个好办法是采用类似高速缓存的技术，将每次下载的文件都临时保存在用户的硬盘上，虽然第一次使用时花的时间比较多，但当再次使用时，只需直接从硬盘上读取文件而无需再与Internet连接，便可以大大提高性能了。

## 调用applet

大家都知道由于java2的安全性，对applet的正常调用的[html文件](https://baike.baidu.com/item/html%E6%96%87%E4%BB%B6)已经不能再使用了，而改为ActiveX类型的调用。具体的又分ie和nescape的不同写法，这一些在sun网上都能找到现成的教程。

### 重要

其中我要强调一点，因为applet每一次的改动都需要重新打包签名，手续非常繁琐，所以在具体的实现中要将一些会变化参数放到[html文件](https://baike.baidu.com/item/html%E6%96%87%E4%BB%B6)中来，传到applet中去，这一点网上文章好多，自己去看吧。

### 缺陷

另外一个就是有朋友问我，那这样不是太麻烦了，每一个客户端都要进行复杂的dos命令操作，我只能说我的水平只能将一个已经做好的客户端文件cer文件和java.policy以及cacerts文件直接拷贝到客户端，当然这也有缺陷，如果别人的计算机已经有了认证，就会丢失。就这些问题我们可以一起探讨。

### 优化

另外还有一点优化，就是在打包的时候，我这儿只讲了把所有要用的涉及到安全性的包和[源程序](https://baike.baidu.com/item/%E6%BA%90%E7%A8%8B%E5%BA%8F)到要打到一个包中。这样如果包非常大的话，会非常影响下载的速度，如果可以使用本地计算机的包就好了，这一点jre也做到了，具体的要到控制面板的jre控制台上去设置。这个就留着读者自己去摸索吧。

## 实现方法

### 服务器端

1．将程序需要用到的各种包文件全部解压(要用到WEBLOGIC的JMS包使用命令jar xf weblogicc.jar)，然后使用JDK的打包命令将编译好的监控程序.class和刚才解压的包一起打包到一个包中。(前提我已经将监控程序和解开的包都放在同一个目录下了)，都是dos状态下的命令，具体命令见jdk1.3(1.2)的bin目录下，

命令如下：

jar cvf monitor.jar *.class

此命令生成一个名为monitor.jar的包

2．为刚才创建的包文件（monitor.jar）创建keystore和keys。其中，keystore将用来存放密匙(private keys)和公共钥匙的认证，alias别名这儿取为monitor。

命令如下：

keytool -genkey -keystore monitor.keystore –alias monitor

此命令生成了一个名为monitor.keystore的keystore文件，接着这条命令，系统会问你好多问题，比如公司名称，地址，要设定的密码等等，都可以随便写。

3．使用刚才生成的钥匙来对jar文件进行签名

命令如下：

jarsigner -keystore monitor.keystore monitor.jar monitor

这个命令将对monitor.jar文件进行签名，不会生成新文件。

4．将公共钥匙导入到一个cer文件中，这个cer文件就是要拷贝到客户端的唯一文件 。

命令如下：

keytool -export -keystore monitor.keystore -alias monitor -file monitor.cer

此条命令将生成monitor.cer认证文件，当然这几步都有可能问你刚才设置的密码。这样就完成了[服务器](https://baike.baidu.com/item/%E6%9C%8D%E5%8A%A1%E5%99%A8)端的设置。这时就可以将jar文件和keystore文件以及cer文件(我这儿是monitor.jar,monitor.keystore,monitor.cer)拷贝到服务器的目录下了。

### 客户端

1． 首先应该安装jre1.3.0_01，然后将[服务器](https://baike.baidu.com/item/%E6%9C%8D%E5%8A%A1%E5%99%A8)端生成的monitor.cer 文件拷贝到jre的特定目录下，我这儿是：

c:\program files\javasoft\jre\1.3.0_01\lib\security目录下。

2． 将公共钥匙倒入到jre的cacerts（这是jre的默认keystore）

命令如下：

> keytool -import -alias monitor -file monitor.cer
>
> -keystore cacerts

注意这儿要你输入的是cacerts的密码，应该是changeit，而不是你自己设定的keystore的密码。

3． 修改policy策略文件，在dos状态下使用命令 policytool

系统会自动弹出一个policytool的对话框，如图4所示，在这里面首先选择file菜单的open项，打开c:\program files\javasoft\jre\1.3.0_01\lib\security目录下的java.poliy文件，然后在edit菜单中选择Change keystore ，在对话框中new keystore url:中输入

file:/c:/program files /javasoft/jre/1.3.0_01/lib/security/cacerts, 这儿要注意反斜杠，在new keystore type 中输入JKS，这是cacerts的固定格式，然后单击Add Policy Entry，在出现的对话框中CodeBase中输入：

> http://URL:7001/*

其中的URL是[服务器](https://baike.baidu.com/item/%E6%9C%8D%E5%8A%A1%E5%99%A8)的IP地址，7001是weblogic的端口，如果是在别的应用服务器上比如说是apache，那[端口号](https://baike.baidu.com/item/%E7%AB%AF%E5%8F%A3%E5%8F%B7)就可以省略掉。

在SignedBy中输入(别名alias):这儿是Monitor然后单击add peimission按钮，在出现的对话框中permission中选择你想给这个applet的权限，这儿具体有许多权限，读者可以自己找资料看看。这儿就选用allpeimission，右边的signedBy中输入别名：monitor

最后保存，在file菜单的save项。

这样客户端的设置就完成了。在客户端用ie运行该applet程序时，会询问你是不是对该签名授权，选择授权后，包会自动从[服务器](https://baike.baidu.com/item/%E6%9C%8D%E5%8A%A1%E5%99%A8)下载到本地计算机，而且ie会自动启动jre，在右下栏中可以看见，相当于ie的java控制台。

4．调用applet的[html文件](https://baike.baidu.com/item/html%E6%96%87%E4%BB%B6)

applet的正常调用的html文件已经不能再用了。

```
<html>

<META HTTP-EQUIV="Content-Type" CONTENT="text/html;CHARSET=gb2312">

<center>
```



### 监控平台

```
<hr>

<OBJECT classid="clsid:8AD9C840-044E-11D1-B3E9-00805F499D93"
	width="900" height="520" align="baseline" codebase="http://192.168.2.217:7001/j2re-			1_3_0_01-win-i.exe#Version=1,3,0,0">
</OBJECT>
</center>
</html>

```



# JSP java script page

<u>类似于加入js，但是在服务器端运行，生成结果直接传送给客户（通常产生的是直接的HTML文本）</u>

[菜鸟jsp教程](http://www.runoob.com/jsp/jsp-tutorial.html)

​	JSP全名为Java Server Pages，中文名叫java服务器页面，其根本是一个简化的[Servlet](https://baike.baidu.com/item/Servlet)设计，它 是由Sun公司倡导、许多公司参与一起建立的一种动态网页技术标准。JSP技术有点类似ASP技术，它是在传统的网页HTML标准通用语言的子集）文件(.htm,html中插入Java程序段(Scriptlet)和JSP标记(tag)，从而形成JSP文件，后缀名为(*.jsp)。 用JSP开发的Web应用是跨平台的，既能在Linux下运行，也能在其他操作系统上运行。

![img](C:\Users\ZhangConghai\Pictures\Typora\e7cd7b899e510fb34315d4d7dd33c895d1430c65.jpg)

​	它实现了Html语法中的java扩展（以 <%, %>形式）。JSP与Servlet一样，是在服务器端执行的。通常返回给客户端的就是一个HTML文本，因此客户端只要有浏览器就能浏览。

​	JSP技术使用Java编程语言编写类XML的tags和scriptlets，来封装产生动态网页的处理逻辑。网页还能通过tags和[scriptlets](#Scriptlet)访问存在于服务端的资源的应用逻辑。JSP将网页逻辑与网页设计的显示分离，支持可重用的基于组件的设计，使基于Web的应用程序的开发变得迅速和容易。 JSP(JavaServer Pages)是一种动态页面技术，它的主要目的是将表示逻辑从Servlet中分离出来。

​	**Java Servlet是JSP的技术基础**，而且大型的Web应用程序的开发需要Java Servlet和JSP配合才能完成。JSP具备了java技术的简单易用，完全的面向对象，具有平台无关性且安全可靠，主要面向因特网的所有特点。

一个JSP页面可以被分为以下几部份：

- 静态数据，如HTML
- JSP指令，如include指令
- JSP脚本元素和变量
- JSP动作
- 用户自定义标签





## Scriptlet

 Scriptlet包含一个有效的程序段。一个scriptlet能够包含多个jsp语句，方法，变量，表达式。

在Web容器处理JSP页面时执行，scriptlet能够包含多个jsp语句.

Scriptlet是包含在<% %>之间的Java代码，在Web容器处理JSP页面时执行，通常会产生输出，并将输出发送到客户的输出流里。

<% %>中除了不能定义类和方法、不能用import引入类外，可以包含任何有效的Java代码。（Java类在Jsp外部定义，可用page指令的import属性引入，也可以Java Bean的形式使用。Java中的方法必须在类内定义，但Jsp允许使用声明定义方法。窗体（GUI）设计代码在Jsp中无效）。

任何文本，HTML标记，JSP元素必须在scriptlet之外，当JSP收到客户的请求时，scriptlet就会被执行，如果scriptlet有显示的内容，这些显示的内容就被存在out对象中。

因为scriptlet,我们便能做以下的事:

* 声明将要用到的变量或方法；
* 编写JSP表达式；
* 使用任何隐含的对象和任何用<jsp:useBean>声明过的对象。

