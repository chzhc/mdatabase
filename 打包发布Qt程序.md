---
typora-copy-images-to: ..\Pictures\Typora
---

# 1.  概述

 当我们用QT写好了一个软件，要把你的程序分享出去的时候，不可能把编译的目录拷贝给别人去运行。编译好的程序应该是一个主程序，加一些资源文件，再加一些动态链接库，高大上一些的还可以做一个安装文件。

QT开发的程序发布的时候经常采用两种方式：

> l  静态编译，可生成单一的可执行文件。
>
> l  动态编译，需同时附上需要的dll文件。

# 2.  发布准备

不管采用哪种方式，首先我们要把项目的发布做一些准备，例如：准备程序的图标，将项目编译成release等。  

## 2.1. 设置程序图标

应用程序的图标分两种：

> l  主窗口图标，主要在程序运行时显示在主窗口左上角，或在任务栏上显示。
>
> l  运行程序图标，一般在桌面或文件夹中显示的缩略图标，可以点击后运行程序。

### 2.1.1.   设置主窗口图标

首先在绘图工具（例如Photoshop）中设计好图标，图标的分辨率最好大于32*32。图标需要用*ico或*png的文件。如果想偷懒一点，直接去[这里](http://www.iconfinder.com/)（http://www.iconfinder.com/）下载，大量好看免费的图标。

将刚才的生成的图标文件放到yourProjectFolder/Resources/images目录下,或者任意一个程序可以找到的位置。最好将图标文件添加到qrc中统一管理，qrc文件的格式如下：

> <RCC>
>
> ​    <qresourceprefix="/background">
>
> ​        <filealias="mainlogo">christmas_stocking.png</file>
>
> ​    </qresource>
>
> </RCC>

然后再主窗口中使用，代码如下，代码很简单，不过记住这句代码一定要放到Mainwindow的构造函数里去，不然不会work。

  /*setwindowicon*/

   setWindowIcon(QIcon(QStringLiteral(":/background/mainlogo")));

程序运行后的效果如下：

![20160105150156624](C:\Users\ZhangConghai\Pictures\Typora/20160105150156624.png)

### 2.1.2.   设置运行程序的图标

设置运行程序的图标在各个平台是各不相同的，下面分别介绍。首先准备分辨率最好大于32*32的图标文件。图标需要用*ico的文件。

**1)       Windows平台**

> **A）使用Visual Studio IDE开发工具**
>
> 如果使用的是VisualStudio IDE开发工具，例如在VS2015中，项目上右击，添加，资源，选择icon，然后将之前准备的*ico图标文件导入，确定之后项目中就会出现一个 projectname.rc 的文件。

![20160105150427417](C:\Users\ZhangConghai\Pictures\Typora/20160105150427417.png)



![20160105150446955](C:\Users\ZhangConghai\Pictures\Typora/20160105150446955.png)







> **B）使用QTCreator的IDE开发工具**
>
> 如果你是使用qmake生成makefile文件或使用QT Creator IDE，那么按下面三步实施：
>
> > l  第一步：创建一个包含图标图像的ICO文件，并将其保存在资源文件目录下，例如命名为：myapp.ico；
> >
> > l  第二步：创建一个.rc文件，包含如下内容：
>
> > > IDI_ICON1               ICON    DISCARDABLE     "myapp.ico"  
>
> > l  第三步：在工程文件myapp.pro文件中写入如下内容：
>
> > > RC_FILE = myapp.rc  
>
> 再次编译程序，即可达到效果，效果图如下：

![20160105150643503](C:\Users\ZhangConghai\Pictures\Typora/20160105150643503.png)

**2)       Mac OS X平台**

> 虽然许多程序可以创建图标文件（.icns），推荐的方法是使用由苹果公司提供的程序iconutil。Iconutil是一个命令行工具，它允许您导入多个不同大小的图标（在不同的上下文中使用），并能压缩文件。在您的项目目录中将所有一系列的图标保存在一个文件中。
>
> 如果您正使用qmake生成makefile文件，您只需要将一个单一的行添加到您的.pro工程文件。例如，如果您的图标文件的名称是myapp.icns，并且您的项目文件名称是myapp.pro，加入这一行到myapp.pro：
>
> > ICON = myapp.icns  
>
> 这将确保qmake把你的图标放在适当的位置并为图标创建一个Info.plist条目。
>
> 如果你不使用qmake，你必须手动执行以下几点：
>
> 1)   为您的应用程序（使用PropertyListEditor，在Developer/Applications中可以找到）创建一个Info.plist文件。
>
> 2)   在Info.plist文件（同样，使用PropertyListEditor）关联您的.icns记录和CFBundleIconFile记录。
>
> 3)   将Info.plist文件复制到你的应用程序包的Contents目录。
>
> 4)   复制.icns文件到你的应用程序包的Contents/Resoures目录。

**3)       通用的Linux平台**

> 在本节中，我们简要地介绍一下在两种常见的Linux桌面环境：KDE和GNOME，为应用程序提供图标的相关问题 。为这两种桌面，用来描述应用程序图标的核心技术是相同的，也可以适用于其他，但具体到每一个来讲也有各自的细节。对使用这些Linux桌面系统的标准信息的主要来源是freedesktop.org。有关其他Linux桌面的信息，请参阅您感兴趣的桌面文档。
>
> 通常情况下，用户不直接使用可执行文件，而是通过点击桌面上的图标来启动应用程序。这些图标是包含带有关于它的图标信息的应用程序的描述表示的“桌面项” 文件。这两种桌面环境都能够在这些文件中检索信息，并且可以用它们来生成应用程序的快捷方式到桌面上，在开始菜单中，或者在面板上。
>
> 有关桌面项文件的更多信息，可以在Desktop EntrySpecification中找到。
>
> 虽然桌面项文件可以有效地封装应用程序的详细信息，我们仍然需要将图标存储在每个桌面环境中的常规位置。用于图标的一些位置在Icon ThemeSpecification中给出了。
>
> 虽然用于定位图标的路径依赖于在桌面上的使用和它的配置，下面所有这些目录结构应该遵循相同的模式：子目录都按照主题，图标大小和应用程序类型进行组织。 通常，应用程序图标被添加到高彩主题，因此方形的应用程序图标的大小为32像素，它将被存储在hicolor/32x32/apps目录的图标路径的下方。

> **1、   K桌面环境(KDE)**

> 应用程序图标可以被所有用户或者一个单一的用户安装使用。当前登录到他们的KDE桌面的用户可以通过使用kde-config发现这些位置，例如，通过在终端窗口中键入执行下列操作：
>
>  
>
> > kde-config --pathicon
>
>  
>
> 通常情况下，被输出到stdout的以冒号分隔的路径列表中包含了用户特定的图标路径和全系统的路径。下面这些目录中，可以根据在Icon ThemeSpecification中描述的约定来定位并安装图标。
>
> 如果您正在专门为KDE开发，你不妨利用KDE build system的优势来配置您的应用程序。这可以确保你的图标被安装在KDE的适当位置。
>
> KDE的开发者网站是<http://developer.kde.org/>。

> **2、   GNOME**

> 应用程序图标都存储在包含与体系结构无关文件的一个标准全系统目录下。这个位置可以通过使用gnome-config来确定，例如，通过在一个终端窗口中键入以下命令：
>
>  
>
> > gnome-config--datadir
>
>  
>
> 输出在标准输出stdout的路径指的是包含一个名为pixmaps目录的位置;这个pixmaps目录中的目录结构中在Icon ThemeSpecification中进行了描述。
>
> 如果您正在专门为GNOME开发，你可能希望使用一组标准的GNU Build Tools，在GTK+/GnomeApplication Development book的相关章节也作了说明。这可以确保你的图标被安装在为GNOME中的适当位置。

## 2.2. 编译release版本

注意将运行程序编译方式设置成Release，因为debug版本的程序中包含了调试信息，可以用来调试。而真正要发布程序时，要使用release版本，这样可以减少发布程序的体积同时增加软件的安全。

![20160105150846511](C:\Users\ZhangConghai\Pictures\Typora/20160105150846511.png)

上图演示在Qt Creator中的设置方式，选中IDE左边的侧边栏的项目，然后再构建设置中将构建配置设置为Release。

![20160105150903113](C:\Users\ZhangConghai\Pictures\Typora/20160105150903113.png)

上图演示在VS2015中的设置方式，选中IDE右边的解决方案管理器中的项目，然后在IDE上边的工具栏中将构建配置设置为Release。

# 3.  发布程序

安装QT SDK 后，默认采用的是动态链接库的编译方式，如果需要发布程序，需要在可执行的文件中添加必须的动态链接库，然而有些动态链接库文件很大，这并不是我们想要的结果。

最好的办法是提交一个静态链接的程序。但是安装的Qt是动态编译的，要生成静态的版本，就需要自己重新进行编译。

## 3.1. 静态编译

> l  优点，发布简单，单一文件。
>
> l  缺点，库文件很大，更新程序版本不方便。每次升级，都要重新分发所有的内容。

静态发布虽然不需要较多的dll，发布简单、方便，但是往往会牵扯到授权问题（详情请查看Qt LGPL授权），动态发布则可以避免。。。如果附带了Qt的dll，就相当于发布了Qt的dll，而這些库是属于Qt的，这足以保证使用者知道程序使用了LGPL版本的Qt。

 

**1)       Windows平台静态编译**

​         静态编译首先需要将

> mingw平台静态编译，在编译Qt的时候，有个configure.exe程序，
>
> configure.exe-static -platform win32-g++
>
> 可以产生静态编译项目文件。然后，
>
> mingw32-makesub-src
>
> 就可以编译出静态库。如果只是用来分发程序，也可以mingw32-makerelease sub-src只编译一个静态库。
>
> VC2015平台，在编译Qt的时候，configure.exe-static-platform win32-msvc2015
>
> 然后nmakesub-src或者nmake release sub-src完成静态编译。
>
> 再然后，就是把你的程序重新用静态编译的Qt再编译一次。
>
> 你会得到一个非常大的可执行程序。推荐使用压缩一下。就可以发给用户了。

 

**2)       linux平台静态编译**

> 1、下载源安装程序，如 qt-everywhere-opensource-src-5.5.1.tar.gz
>
> 2、解压到某一目录
>
> 3、cd 进入解压后的目录，命令：
>
>  
>
> ./configure-static -release -qt-zlib -qt-gif -qt-libpng -qt-libmng -qt-libjpeg -nomakedemos -nomake examples -qt-sql-sqlite -prefix /usr/local/Trolltech/Qt-5.5.1_static
>
>  
>
> 参数-static 指的是要采用静态编译Qt的方法
>
> 参数-release指的是只编译release版本的库
>
> 参数-qt-zlib -qt-gif-qt-libpng -qt-libmng -qt-libjpeg 是更好确定Qt编译出的程序能在目前不知的系统下运行。
>
> 参数-nomake demos-nomake examples 指的是不编译demos和examples
>
> 参数-qt-sql-sqlite 如果没有这个参数，configure的时候，可能会提示sqlite 有问题而中止。
>
> 参数 -prefix/usr/local/Trolltech/Qt-5.5.1_static 指明静态编译的Qt安装的目录，命名Qt-5.5.1_static是为了区别动态编译安装的Qt，因为如果没有这个参数，安装时会覆盖之前动态编译安装的Qt（如果有的话）。
>
> u  注:  如果出现错误：Basic XLibfunctionality test failed!
>
> You might need to modify theinclude and library search paths by editing
>
> QMAKE_INCDIR_X11 andQMAKE_LIBDIR_X11
>
> 解决办法：yum installlibX*  
>
> （如果是Ubuntu也可运行sudo apt-getinstall libx11-dev libxext-dev libxtst-dev)
>
> 4、没问题后
>
>  
>
> make
>
>  
>
> 5、make没问题后再 
>
>  
>
> sudo make install
>
>  
>
> 6、增加（或改变）环境变量： （也可以不添加环境变量，使用绝对路径进行编译），在你home目录下你的的名字的目录中,在.profile文件中追加（或改变）如下环境变量：(为Qt设置一些特定的环境变量，这个很重要！.profile文件是隐藏的，可在你的名字目录下，按Ctrl+H显示所有文件查看到)
>
> QTDIR=/usr/local/Trolltech/Qt-5.5.1_static/   
>
> PATH=$QTDIR/bin:$PATH 
>
> MANPATH=$QTDIR/doc/man:$MANPATH   
>
> LD_LIBRARY_PATH=$QTDIR/lib:$LD_LIBRARY_PATH
>
> export QTDIR PATHMANPATH LD_LIBRARY_PATH  
>
> 7、重启
>
> 8、在终端测试一下qmake

 

**3)       MAC OS X系统平台静态编译**

> 在MAC OS X系统平台下静态编译QT应用还是比较麻烦的，首先需要静态编译QT应用依赖的各个库，然后再进行连接生成运行文件，具体步骤参看链接：<http://doc.qt.io/qt-5/osx-deployment.html>。

 

## 3.2.  附加Dll文件

> l  优点，更新方便，发布多个产品时，可以统一使用一个库。
>
> l  缺点，文件多、杂。

Qt官方开发环境默认使用动态链接库方式，在发布生成的可执行程序时，我们需要复制一大堆动态库，如果自己去复制动态库，很可能丢三落四，导致程序在别的电脑里无法正常运行。 因此 Qt 官方开发环境里自带了一个部署工具来帮助开发者自动拷贝大部分的依赖库。在不同的平台使用方式也有所不同。

**1)       Windows平台**

> Windows开发环境下windeployqt工具 (如果你已经将Qt的bin目录加入PATH环境,那么你可以直接在命令行使用windeployqt调用.)。首先，将项目中的release文件中的可执行文件拷到一个新建的文件夹中，例如project.exe，用Qt自带的生成必备的dll文件的程序windeployqt，来把必要的动态库拷到该文件夹中，打开命令行，输入windeployqtproject.exe，这时候大部分的dll文件都自动拷贝过来了，但是如果项目还用了一些其他的SDK，比如OpenCV，Chartdir51等等，就需要手动将所需dll拷贝过来，如果不知道还需要哪些软件，可以用Dependency Walker来查看缺少哪些dll文件。
>
> ![20160105151420720](C:\Users\ZhangConghai\Pictures\Typora/20160105151437312.png)
>
> ![20160105151420720](C:\Users\ZhangConghai\Pictures\Typora/20160105151420720.png)
>
> 拷贝完成后文件夹下的文件清单如下：
>
> ![img](https://img-blog.csdn.net/20160105151437312?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
>
> **注意：如果发布的应用是Qt Quick Application应用，那么命令行需要加上QML的安装目录。命令中的D:\Qt\Qt5.5.1\qml是qml的安装目录,请换成你自己的qml安装目录!!!!!**
>
> > windeployqt hello.exe--qmldir D:\Qt\Qt5.5.1\qml
>
> 接下来要高大上的朋友就可以用Enigma VirtualBox软件虚拟化工具将多个文件封装到应用程序主文件，从而制作成为单执行文件的绿色软件。

**2)       linux平台**

> 在X11平台下qt程序，首先准备好程序中需要使用的资源，库和插件...，比如你的可运行程序取名叫作panel，那把你的panel,那些libQt*.so.4和libQt*.so.4.6.0（链接和共享库都要）放在同一目录下(也可以不同,只要小小修改下shell文件).plugins就不多说了。
>
> 在程序的同目录下，新建一個空文档，取名panel.sh (文件名与程序名同名,扩展名为sh,shell文件)。在panel.sh中原封不动的写入以下语句:  
>
> \#!/bin/sh
>
> appname=`basename $0 | sed s,/.sh$,,`
>
>   dirname=`dirname$0`
>
>   tmp="${dirname#?}"
>
>   if ["${dirname%$tmp}" != "/" ]; then
>
>    dirname=$PWD/$dirname
>
>  fi
>
>    LD_LIBRARY_PATH=$dirname
>
> export LD_LIBRARY_PATH
>
>    $dirname/$appname$*
>
>  
>
> 保存文件，退出。在终端給文件+x属性: 切换到程序的目录，输入
>
>    chmod +x panel.sh
>
>  
>
> 然后运行shell文件就行了（确保panel程序具备X属性）,它会自动更改环境变量,运行程序。
>
> 如果要调试shell文件,只需要在终端输入:
>
>    sh -x panel.sh
>
>  
>
> 这样就ok了。
>
> **如果需要把执行程序制作成DEB或RPM包的话请参考链接：**
>
> Deploying aQt5 Application Linux： <https://wiki.qt.io/Deploying_a_Qt5_Application_Linux>
>
>  

**3)       MAC OS X系统平台**

> 由于Qt的库并不是OS X标配的，所以我们要自己去复制库到app包里，才可以让app在其他未安装Qt的电脑上运行。
>
> 比较幸运的是，Qt为我们提供了macdeployqt工具，借助于此，在OS X上发布Qt写的程序几乎是所有平台中最简单的。
>
> 注：我电脑配置了Qt的bin环境，所以可以直接使用macdeployqt，如果没配置，可以用绝对路径去找。
>
>  
>
> 1.发布widgets程序
>
> > a)       这个比较方便。选择Release模式，编译
> >
> > b)       运行macdeployqt
> >
> > ​     对于我的工程，命令是：
> >
> > macdeployqt /Users/Ocean/Desktop/build-untitled-Desktop_5_5_1_64bit-Release/untitled.app-dmg
> >
> > 然后回车，就打包好了。之后我们会发现，app目录下多了一个dmg文件
> >
> > 此dmg文件，里面的app就是我们发布的app了。把dmg拷贝给别人，别人就可以直接使用了。
> >
> > l  **注：如果直接拷贝app文件给别人，别人是无法直接运行的，会有权限问题（要用chmod给可执行文件加上x权限才可以运行）。而压缩过（zip或者dmg）后，拷贝给别人，别人是可以直接运行，没有权限问题。**
> >
> > l  **注：-dmg的意思就是在拷贝好库后，生成一个dmg文件，可以不加这个参数。**
>
>  
>
> 2.发布quick2程序
>
> > ​    这个相对麻烦一点
> >
> > a)       选择Release模式，编译
> >
> > b)       打开终端，先切换编译的目标目录下
> >
> > ​        对于我的工程，命令是：
> >
> > ​           cd /Users/Ocean/Desktop/build-untitled-Desktop_5_5_1_64bit-Release
> >
> > ​    
> >
> > c)       运行macdeployqt
> >
> > ​        对于我的工程，命令是：
> >
> > ​            macdeployqt untitled.app-qmldir=../untitled -dmg
> >
> > ​        然后回车，就打包好了。
> >
> > l  **注1：和widgets发布程序不同，untitled.app 这个名字，要直接输入，不要写 ./untitled.app 或者是其他的 绝对/相对 路径，不然打包出来的文件无法使用！！会报错！！！**
> >
> > l  **注2：-qmldir=../untitled的意思就是说在../untitled 目录下有qml文件，让macdeployqt去分析它们，把要用的库找过来。**