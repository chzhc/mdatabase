---
typora-root-url: ./pic
---

[TOC]



# C++备忘

## 事件驱动

1、确定响应事件的元素

2、为指定元素确定需要响应的事件类型

3、为指定元素的指定事件编写相应的事件处理程序

4、将事件处理程序绑定到指定元素的指定事件

==构建回调机制==

绑定监听事件，节省系统资源



## 友元（friend）

类内可以声明把外部X作为友元，从而实现外部函数（类）对私有类的访问







## 函数模板&类模板

```函数模板
template <typename T> 	//未定变量类型
inline T const& Max (T const& a, T const& b) 
{ 
    return a < b ? b:a; 
} 
```

```类模板
template <class T> 	//未定变量类型
class Stack { 
  private: 
    vector<T> elems;     // 元素 
  public: 
    void push(T const&);  // 入栈
    void pop();               // 出栈
    T top() const;            // 返回栈顶元素
    bool empty() const{       // 如果为空则返回真。
        return elems.empty(); 
    } 
}; 
```

## 命名空间 namespace

namespace 允许嵌套例如

```namespace
namespace ns1{
    namespace ns2{
        class widget{
            
        };
    }
}

//因此可以产生形如ns1::ns2::widget方式的引入
//引入通过using namespace ns1::ns2::widget

```

---

## QString 的参数

**QString::arg()//用字符串变量参数依次替代字符串中最小数值**

```c++
QString i = "iTest";           // current file's number  
QString total = "totalTest";       // number of files to process  
QString fileName = "fileNameTest";    // current file's name  
  
QString status = QString("Processing file %1 of %2: %3")  
                .arg(i).arg(total).arg(fileName);  
qDebug() << status ;
```





# 编译知识

## 编译器编码区别



| 字符集                                  | 解释                                                     |
| --------------------------------------- | -------------------------------------------------------- |
| 源码字符集(the source character set)    | 源码文件是使用何种编码保存的                             |
| 执行字符集(the execution character set) | 可执行程序内保存的是何种编码(程序执行时内存中字符串编码) |

平台差异

### GCC

在GCC下，这两个都可以使用你自己喜好的编码(如果不指定，默认都是UTF8)

```
-finput-charset=charset
-fexec-charset=charset
```

除了前两个选项外，还有一个：

```
-fwide-exec-charset=charset
```

### MSVC

MSVC没有类似前面的选项。

| 源码字符集如何解决？ | 有BOM么，有则按BOM解释，无则使用本地Locale字符集(随系统设置而变) |
| -------------------- | ------------------------------------------------------------ |
| 执行字符集如何解决?  | 使用本地Locale字符集(随系统设置而变)                         |

挺霸道哈（当然，源码中可以使用`#pragma setlocale("...")`，但功能很有限，比如Windows没有utf8的locale，所以...）。

另外，和GCC对应的wide-exec-charset呢？

### UTF-8方案

- 如果我们将源码保存成utf8，执行字符集也选为utf8，那么天下将太平了。使用非ASCII字符的源码文件也就可以在不同国家的用户间无障碍流通了 `;-)`.

- 源码保存成UTF-8没有什么困难，但是，**执行字符集需要是UTF-8**。没那么简单

  对GCC来说，这个问题很简单(默认的编码选项足够了)：

- 只要源码文件保存成utf8即可(带或不带BOM均可)

- 早期的gcc不接收带BOM的utf8源码文件，现在，至少在GCC4.6中，这一限制不再存在。

对MSVC来说，这个问题异常复杂：

- 对MSVC2003来说，只要源码保存成不带BOM的utf8即可
- 对MSVC2005、(没在SP1基础上装热补丁的)MSVC2008来说。完全没办法
- 直到MSVC2010sp1，才算提供了一个解决方案。源码保存成带BOM的utf8，utf16，...，然后添加

```在需特定执行编码的文件上附加编译指令
#pragma execution_character_set("utf-8")
```

要想跨GCC4.6+和MSVC2010sp1+，我们需要取它们的交集：也就是

- 源码保存成带BOM的utf8
- 为MSVC添加`#pragma`

### C++11执行字符集

前面提到的u8就是C++11为“执行字符集”所做的努力之一。

新明确规定了utf8、utf16和utf32这3种执行字符集。

| char*     | u8"中文" |
| --------- | -------- |
| char16_t* | u"中文"  |
| char32_t* | U"中文"  |

可是C++11并没有规定源码字符集

```
const char* mystr=u8"中文";
```

C++标准对编译器说，我不管这个文件的具体编码是什么，但你必须给我生成对应utf8编码的字节流。

编译器似乎有点傻了吧？不知道源文件的编码，我如何转换

于是：

**MSVC说：源码文件必须有BOM，不然我就认为你是本地locale的编码**

GCC说：我认为你就是utf8编码，除非通过命令行通知我其他编码

在C++11标准下，对源码编码 简单的处理办法还是，使用带BOM的UTF8保存。

---



# QT

##QT安装组件

![t 组](C:\Users\ZhangConghai\Pictures\Typora/Qt 组件.png)

## 编译选项



```Qt .pro设置
# 编译注释
PRECOMPILED_HEADER = mainwindow.h
QMAKE_CXXFLAGS += /MP
```

## Layout管理

 Qt有一系列的布局管理类，用来描述在交互界面上这些子部件如果排列。当子部件的位置、大小改变后，这些个布局管理类会自动处理子部件的空间位置、大小，使之可以整齐的排列起来。

​      所有的QWidget子类都可以使用布局管理类来排列其子widget。调用QWidget::setLayout()函数可以指定该widget使用什么样的布局方式。当某个widget被指定布局时，该布局将会自动的完成如下的动作：

- 定位子widget的位置
- 设置windows的默认大小
- 设置windows的最小大小
- 重新调整大小
- 当如下部分更改时自动调整更新：

​               (1) 字号、text内容

​               (2) 显示/隐藏某widget

​               (3) 删除某widget

### Qt的布局管理类

​      Qt的这些布局管理类是专门为手敲代码进行布局而设计的，并且布局的精度以像素为单位。Qt Designer这种可视化布局的时候也同样使用了这些布局管理类。用Qt Designer设计表单有个好处，就是这些布局避免了编译、链接、循环运行。

- **QGraphicsAnchor**（在QGraphicsAnchorLayout中表示两个item的锚点）；
- **QGraphicsAnchorLayout**（在Graphics View中一个item链接到widget的锚点的布局）；
- **QBoxLayout**（水平/垂直布局）；
- **QHBoxLayout**（水平布局）；
- **QVBoxLayout**（垂直布局）；
- **QFormLayout**（表单布局）；
- **QGridLayout**（网格布局）；
- **QLayout**（布局的基类）；
- **QLayoutItem**（布局中的抽象项目）；
- **QSpacerItem**（布局中的空格）；
- **QWidgetItem**（布局中的widget项目）；
- **QSizePolicy**（布局策略，描述了水平/垂直布局时的策略）；
- **QStackedLayout**（堆栈widget，同一时刻只有一个widget可显示）；
- **QButtonGroup**（管理一堆按钮的容器）；
- **QGroupBox**（有标题的组合框）；
- **QStackedWidget**（堆栈widget）；

水平布局、垂直布局、网格布局、表单布局

​      最简单的使用布局的方法就是用QHBoxLayout、QVBoxLayout、QGridLayout、QFormLayout来给需要的widget设置布局。这些类都继承于QLayout，而QLayout又继承于QObject。要想设置更加复杂的布局，可以嵌套使用。

![img](https://pic1.zhimg.com/80/v2-32c54eee3fad8497cb938d756a271781_hd.jpg)

**示例代码**

**当你使用某个布局时，构建widget的时候不需要指定父对象（parent）**。layout会自动的为其重新分配父对象。layout不是父对象，==widget只能令其他widget作为父对象，不可以将layout作为父对象。==

### **将widget加入到某个布局中**

​      当你把某个widget加入到某个布局中时，布局会完成如下动作：

1. 所有的widget都先按照各自的QWidget::sizePolicy()和QWidget::sizeHint()分配空间；
2. 如果某个widget设置了stretch factor（拉伸因子），且大于零，则会按照拉伸因子的比例分配空间；
3. 如果某个widget的拉伸因子等于零，则只有在其他widget不需要空间的时候才会占据这部分空间；
4. 若空间小于某widget的minimum size（最小尺寸）时，会按照这个最小尺寸分配。假如没有设置minimum size，会按照minimum size hint分配最小空间。widgets其实不需要设置minimum size或者minimum size hint，stretch factor（拉伸因子）反而是最为重要的。
5. 若空间大于某widget的maximum size（最大尺寸）时，会按照这个最大尺寸分配。同样的，拉伸因子是最为重要的。

**Stretch Factor（拉伸因子）**

​      widget一般都没有设置stretch factor，这个是用来设置当前widget和其他widget分配空间的比例。

![img](https://pic1.zhimg.com/80/v2-877978a11c26eccb206299760f106965_hd.jpg)

### **为自定义widget设置布局**

​      当你自定义一个QWidget后，还应该传递出该widget的布局属性。假如你自定义的这个widget没有子widget，或者说你对其手动进行了布局，你可以使用如下的机制来改变widget的行为：

- 重新实现QWidget::sizeHint()来返回首选尺寸
- 重新实现QWidget::minimumSizeHint()来返回允许的最小尺寸
- 调用QWidget::setSizePolicy()设置widget的布局要求

​      无论什么时候，只要size hint、minimum size hint、size policy改变时，都要调用QWidget::updateGeometry()来使布局重新计算各部分的尺寸来更新。不管调用QWidget::updateGeometry()多少次，只要那三个不变，都只会计算一次。

​      当自定义的widget的高度取决于实际宽度时（例如具有自动断字功能的label），需要在widget的size policy（尺寸策略）中设置“height-for-width”flag，并重新实现QWidget::heightForWidth()函数。

### **布局中的问题**

​      在label widget中使用rich text的时候会产生一些布局问题。假如这个label是文字，而Qt布局管理处理rich text时就会发生问题。

​      在一些情况下，父layout是QLayout::FreeResize模式，这就导致布局无法适应其内容而调整尺寸，甚至导致用户无法在小尺寸上进行操作。解决的办法就是子类化出现问题的widget，然后重新实现sizeHint()、minimumSizeHint()函数。

​      在某些情况下，比如设置QDockWidget或者QScrollArea的widget时，必须在widget已经有个布局存在，否则的话这个widget不可见。

### **手动布局**

​      如果你自定义个特殊的布局，还可以按照上述方法自定义一个widget。重新实现QWidget::resizeEvent()来计算所需空间，并调用每个子widget的setGeometry()函数。当widget需要重新计算时，它会接收**QEvent::LayoutRequest**类型的事件。重新执行QWidget::event()来处理QEvent::LayoutRequest事件。







## 信号槽结构



```signal&slot
#1 需要类内引入信号槽的宏 Q_OBJECT
#2 类内部定义 signals: 
eg.
signals:
	void sendsignal();
//可以直接使用Q_SIGNALS: 宏
//定义槽slots
也可以直接使用Q_SLOT:
public Q_SLOTS: // Or alternatively Q_SLOT prefix
void setValue(int value) { if (value != m_value) m_value = value; }




```



![IM截图2018040211135](C:\Users\ZhangConghai\Pictures\Typora/TIM截图20180402111355.png)



![IM截图2018040211170](C:\Users\ZhangConghai\Pictures\Typora/TIM截图20180402111703.png)



## 定时器

```定时刷新
使用信号槽结构设置定时器定时激活时间显示槽函数
QTimer *timer = new QTimer(this);  
//设置timer溢出时间
timer->start(1000); // 每次发射timeout信号时间间隔为1秒
connect(timer,SIGNAL(timeout()),this,SLOT(timerUpdate()));  

//此处timer的信号emit内建为timeout函数，需要在自定窗体类内自建处理槽Slot
声明
public slots:  
    void timerUpdate(void);  
声明也可采用
public Q_SLOTS:
	void timerUpdate();
定义:
void MainWindow::timerUpdate(void)  
{  
    QDateTime time = QDateTime::currentDateTime();  //读取当前时间，然后显示
    QString str = time.toString("yyyy-MM-dd hh:mm:ss dddd");  
    ui->label->setText(str);  
}  

```

### 定时器的应用2

重写widget类的时间处理函数 `void timerEvent(QTimerEvent *p)`

通过`id = startTimer(1000)` 的方式 

```c++
//在重写的时间处理函数中处理
//通过判断定时器的id执行不同操作
void Widget::timerEvent(QTimerEvent *event){
    if (event->timerId() == id1 ){
        ui->label->setText(tr("%1").arg(qrand()%10));
    }
    if (event->timerId() == id2 ){
        ui->label->setText("timer2 overflow");
    }
    else {
        
    }
}

```

 



## 手工绘图

==可以使用多线程绘图QPaint允许==

### 绘制简单图形

```c++
void MainWindow::paintEvent(QPaintEvent *)
{
    QPainter painter(this);
    painter.drawLine(QPointF(0, 0), QPointF(100, 100));
}
```

允许重写`paintEvent`函数

通过建立画笔`QPainter`对象调用画笔的函数实现绘制

![img](C:\Users\ZhangConghai\Pictures\Typora\11-2.jpg)

`QPainter`对象可以设置对应的画笔(`QPEN`)以及画刷`QBrush`

画笔以及画刷可以设置不同的样式使用`.setStyle()`设置

![img](C:\Users\ZhangConghai\Pictures\Typora\11-5.jpg)

![img](C:\Users\ZhangConghai\Pictures\Typora\11-6.jpg)



![img](C:\Users\ZhangConghai\Pictures\Typora\11-7.jpg)

Qt的预定义颜色

#### 渐变(gradient)

渐变类包括 **通过brush或者pen的`.setStyle(QGradient)`的方式设置

- 线性渐变`(QLinearGradient)`

  ![img](C:\Users\ZhangConghai\Pictures\Typora\12-2.jpg)

- 辐射渐变`QRadialGradient `

  ![img](C:\Users\ZhangConghai\Pictures\Typora\12-4.jpg)

- 锥形渐变`QConicalGradient `

  ![img](C:\Users\ZhangConghai\Pictures\Typora\12-5.jpg)

### 绘制文字

```c++
//基本绘制在100,100处显示文字
void MainWindow::paintEvent(QPaintEvent *)
{
    QPainter painter(this);
    painter.drawText(100, 100, "qter.org_yafeilinux");
}
```

![img](C:\Users\ZhangConghai\Pictures\Typora\13-2.jpg)

它的第一个参数指定了绘制文字所在的矩形；第二个参数指定了文字在矩形中的对齐方式，它由`Qt::AlignmentFlag`枚举变量进行定义，不同对齐方式也可以使用`|`操作符同时使用，这里还可以使用`Qt::TextFlag`定义的其他一些标志，比如自动换行等；第三个参数就是所要绘制的文字，这里可以使用`\n`来实现换行；第四个参数一般不用设置。

```c++
void MainWindow::paintEvent(QPaintEvent *)
{
    QPainter painter(this);
    //设置一个矩形
    QRectF rect(20, 20, 300, 200);
    //为了更直观地看到字体的位置，我们绘制出这个矩形
    painter.drawRect(rect);
    painter.setPen(QColor(Qt::red));
    //我们这里先让字体水平居中
    painter.drawText(rect, Qt::AlignHCenter, "yafeilinux");
}
```

#### 文字对齐方式(align)![img](C:\Users\ZhangConghai\Pictures\Typora\13-4.jpg)

#### 文字字体(font)

```C++
//设置字体
QFont font("宋体", 15, QFont::Bold, true);
//设置下划线
font.setUnderline(true);
//设置上划线
font.setOverline(true);
//设置字母大小写
font.setCapitalization(QFont::SmallCaps);
//设置字符间的间距
font.setLetterSpacing(QFont::AbsoluteSpacing, 10);
//使用字体
painter.setFont(font);
painter.setPen(Qt::green);
painter.drawText(120, 80, tr("yafeilinux"));
painter.translate(50, 50);
painter.rotate(90);
//画面旋转
painter.drawText(0, 0, tr("helloqt"));
```

### 绘制路径(QPainterPath)

如果要绘制一个复杂的图形，尤其是要重复绘制这样的图形，那么可以使用`QPainterPath`类，然后使用`QPainter::drawPath()`来进行绘制。`QPainterPath`类为绘制操作提供了一个容器，可以用来创建图形并且重复使用。一个绘图路径就是由多个矩形、椭圆、线条或者曲线等组成的对象，一个路径可以是封闭的，例如矩形和椭圆；也可以是非封闭的，例如线条和曲线。

```c++
void MainWindow::paintEvent(QPaintEvent *)
{
    QPainterPath path;
    //关键在于设置路径对象QPainterPath
    path.addEllipse(100, 100, 50, 50);
    path.lineTo(200, 200);
    QPainter painter(this);
    painter.setPen(Qt::blue);
    painter.setBrush(Qt::red);
    painter.drawPath(path);
}
```

#### 复制路径

如果只是简单的将几个图形拼接在一起，其实完全没有必要用路径，之所以要引入路径，就是因为它的一个非常有用的功能：复制图形路径。我们在`painEvent()`函数中继续添加下面几行代码：

```c++
QPainterPath path2;
//添加原有路径
path2.addPath(path);
path2.translate(100,0);
//向x右转移100
painter.drawPath(path2);
```

### 绘制图片(QPixmap)

主要使用QPixmap读取图片,然后使用QPainter的`.drawPixmap()`函数显示

#### 平移图片(translate)

```c++
painter.translate(100, 100); //将（100，100）设为坐标原点,是QPainter 的属性
painter.drawPixmap(0, 0, 129, 66, pix);
```

#### 缩放图片(scaled)

```c++
qreal width = pix.width(); //获得以前图片的宽和高
qreal height = pix.height();
//将图片的宽和高都扩大两倍，并且在给定的矩形内保持宽高的比值不变
pix = pix.scaled(width*2, height*2, Qt::KeepAspectRatio);
painter.drawPixmap(70, 70,pix);
```

#### 旋转图片(rotate)

使用`QPainter`的`.rotate(degree)`函数

#### 扭曲图片(shear)

通过`shear(qreal sh，qreal sv)`从h和v两个方向扭曲

### 坐标系统

Qt中每一个窗口都有一个坐标系统，默认的，窗口左上角为坐标原点，水平向右依次增大，水平向左依次减小，垂直向下依次增大，垂直向上依次减小。原点即为`（0，0）`点，以像素为单位增减。

#### 坐标系统变换

默认的，`QPainter`在相关设备的坐标系统上进行绘制，在进行绘图时，可以使用`QPainter::scale()`函数缩放坐标系统；使用`QPainter::rotate()`函数顺时针旋转坐标系统；使用`QPainter::translate()`函数平移坐标系统；还可以使用`QPainter::shear()`围绕原点来扭曲坐标系统。如下图所示。

![img](C:\Users\ZhangConghai\Pictures\Typora\16-2.jpg)

==相当于设置QPainter的属性,不手动更改回去的话就成为默认值==

#### 扭曲(shear)

`shear()`有两个参数，第一个是对横向进行扭曲，第二个是对纵向进行扭曲，而取值就是扭曲的程度。比如程序中对纵向扭曲值为1，那么就是红色正方形左边的边下移一个单位，右边的边下移两个单位，值为1就表明右边的边比左边的边多下移一个单位。大家可以更改取值，测试效果。

#### 旋转(rotate)



#### 坐标系统的保存(save)

我们可以先利用`save()`函数来保存坐标系现在的状态，然后进行变换操作，操作完之后，再用`restore()`函数将以前的坐标系状态恢复，其实就是一个入栈和出栈的操作。下面来看一个具体的例子，更改`paintEvent()`函数如下：

```c++
void Dialog::paintEvent(QPaintEvent *)
{
    QPainter painter(this);
    painter.save(); //保存坐标系状态
    painter.translate(100,100);
    painter.drawLine(0, 0, 50, 50);
    painter.restore(); //恢复以前的坐标系状态
    painter.drawLine(0, 0, 50, 50);
}
```

#### 获取坐标信息(pos)

重写`mousePressEvent`事件

```c++
void Dialog::mousePressEvent(QMouseEvent *event)
{    
         qDebug() << event->pos();
}
```

#### 绘图设备的坐标系统(Pixmap及Image)

使用`QPixmap`作为对象初始化`QPainter`

![img](C:\Users\ZhangConghai\Pictures\Typora\16-14.jpg)

第一，`QWidget`和`QPixmap`各有一套坐标系统，它们互不影响。可以看到，无论画布在窗口的什么位置，它的坐标原点依然在左上角，为`（0,0）`点，没有变。

### 涂鸦板

实现画板要重写的函数有

```c++
    void paintEvent(QPaintEvent *);
    void mousePressEvent(QMouseEvent *);
    void mouseMoveEvent(QMouseEvent *);
    void mouseReleaseEvent(QMouseEvent *);
```

```c++
//变量有
QPixmap pix;
QPoint lastPoint;
QPoint endPoint;
```

```c++
void Dialog::paintEvent(QPaintEvent *)
{    
      QPainter pp(&pix);    // 根据鼠标指针前后两个位置就行绘制直线    
      pp.drawLine(lastPoint, endPoint);    // 让前一个坐标值等于后一个坐标值，这样就能实现画出连续的线    
      lastPoint = endPoint;    
      QPainter painter(this);    
      painter.drawPixmap(0, 0, pix); 
}
```

这里使用了两个点来绘制线条，这两个点在下面的鼠标事件中获得。

```c++
void Dialog::mousePressEvent(QMouseEvent *event)
{   
               if(event->button()==Qt::LeftButton) //鼠标左键按下       
               lastPoint = event->pos(); 
}
```

当鼠标左键按下时获得开始点。

```c++
void Dialog::mouseMoveEvent(QMouseEvent *event)
{    
         if(event->buttons()&Qt::LeftButton) //鼠标左键按下的同时移动鼠标    
        {        
                  endPoint = event->pos();        
                  update(); //进行绘制    
         }
}
```

当鼠标移动时获得结束点，并更新绘制。调用`update()`函数会执行`paintEvent()`函数进行重新绘制。

```c++
void Dialog::mouseReleaseEvent(QMouseEvent *event)
{   
            if(event->button() == Qt::LeftButton) //鼠标左键释放    
             {        
                      endPoint = event->pos();        
                      update();    
             }
}
```

当鼠标按键释放时也进行重绘。



























































## 文件保存

文件的保存路径有多种形式：

​     ①：如果直接写成 pix.save("a.png"); 或者QFile file("a.PNG"); 则a.png这个文件是创建在和该段代码所在cpp文件同目录下。

​     ②：如果写成pix.save("./debug/a.png")或者QFile file("./debug/a.PNG"); 则a.png这个文件是创建在和代码所在cpp文件同目录下的debug文件夹下。

​     ③：如果写成pix.save("C:/a.png");或者QFile file("C:/a.png"); 则该文件是创建在C盘根目录下。

 

​     亦即：此时创建路径既可以用相对路径，也可以用绝对路径。



## 根据窗口分配的大小重画resizeEvent

```c++
// mainwindow.h中
private:
    virtual void resizeEvent(QResizeEvent * event);

// mainwindow.cpp中
void MainWindow::resizeEvent(QResizeEvent *event)
{
   QWidget::resizeEvent(event) ;
   if( ui->graphicsView->rect().height()>100){//用来防止窗口大小使用默认值
       ui->graphicsView->setSceneRect(ui->graphicsView->rect());
       // to do something...
   }
}

```















## 对话框

### 文件打开对话框

```c++
QString getOpenFileName ( QWidget * parent = 0, const QString & caption = QString(), const QString & dir = QString(), const QString & filter = QString(), QString * selectedFilter = 0, Options options = 0 )  
```

```c++
	QString fileName;
	fileName = QFileDialog::getOpenFileName(this,
		tr("Open Config"), "", tr("SCD Files (*.scd);; CID Files (*.cid)"));

	if (!fileName.isNull())
	{
            //fileName即是选择的文件名
	}
        else
            //点是的取消
```

### 文件保存对话框

```c++
QString getSaveFileName ( QWidget * parent = 0, const QString & caption = QString(), const QString & dir = QString(), const QString & filter = QString(), QString * selectedFilter = 0, Options options = 0 )  
```

```c++
QString fileName;  
fileName = QFileDialog::getSaveFileName(this,  
    tr("Open Config"), "", tr("Config Files (*.ifg)"));  
  
if (!fileName.isNull())  
{  
                    //fileName是文件名  
         }  
        else  
                     //点的是取消  
```

设置文件过滤的示例

```c++
"Image Files (*.png *.jpg *.bmp)"  //多个文件使用空格分隔

"Images (*.png *.xpm *.jpg);;Text files (*.txt);;XML files (*.xml)"   //多个过滤使用两个分号分隔
```











## 使用信号槽为label添加点击事件

https://blog.csdn.net/mangobar/article/details/52278718

> label 或者 窗口不是BUTTON类的，没有clicked信号，你可以重写mousePressEvent,自己自己发出这个信号，声明信号clicked,在mousePressEvent中 emit clicked（）.至于连接就跟别的信号槽一样了。





## 事件过滤器Event Filter*

```
Any Qt object, re-implementing the eventFilter() function and installed to one or more Qt objects
› bool eventFilter(QObject *receiver, QEvent *event)
› This method returns true if the filter handles the given event, false otherwise
› QObject::installEventFilter(QObject *targetObject)
```



### 通过处理鼠标事件实现悬浮显示

```
bool FramelessWindowCentralWidget::eventFilter(QObject *watched, QEvent *e) {
    // 鼠标进入或离开关闭按钮时设置为不同状态的图标
    if (watched == ui->closeButton) {
        if (e->type() == QEvent::Enter) {
            ui->closeButton->setIcon(QIcon(":/icon/close-hover.png"));
        } else if (e->type() == QEvent::Leave) {
            ui->closeButton->setIcon(QIcon(":/icon/close.png"));
        }
    }
    return QWidget::eventFilter(watched, e);
}


///要注册事件过滤器
button->installEventFilter(this);
```







## 绘制自定义界面



```自定义无边框widget形状
Widget::Widget(QWidget *parent)
: QWidget(parent)
{
this->setWindowFlags(Qt::FramelessWindowHint|Qt::WindowMinimizeButtonHint);
this->setAttribute(Qt::WA_TranslucentBackground);
pix.load(":/new/prefix1/201409091359143092.png");
this->resize(pix.size());    
}
void Widget::paintEvent(QPaintEvent *e)
{
QPainter painter(this);
painter.drawPixmap(0,0,pix);
}
```

##绘制自定义按钮



```自定义按钮形状
//设置按钮形状
QPixmap pix;  
pix.load(":/new/prefix1/111.png");  
ui->pushButton->setFixedSize(pix.size());  
ui->pushButton->setMask(pix.mask());  
ui->pushButton->setStyleSheet("background-image: url(:/new/prefix1/111.png)");  

//此外可以使用QBrush以及QRect等绘制简单图形
使用pix的mask
QPixmap pixmapLeft(":/MainWindow/Resources/Images/TestLeft.png");  
ui.btnLeft->setMask(pixmapLeft.mask());  
QPixmap pixmapRight(":/MainWindow/Resources/Images/TestRight.png");  
ui.btnRight->setMask(pixmapRight.mask()); 
QLabel topLevelLabel;  
QPixmap pixmap(":/images/tux.png");  
topLevelLabel.setPixmap(pixmap);  
topLevelLabel.setMask(pixmap.mask());  

```





### QPixmap

```
QPixmap pix;
pix.load(":/big.jpg")
pix=pix.scaled(width,height)
```







## 样式表StyleSheet

*StyleSheet* 样式表==注意StyleSheet在应用的时候会重置之前的样式表==

### 样式规则Style Rules

```simple1
QPushButton { color: red }
```

Qt Style Sheet is generally case insensitive (i.e., `color`, `Color`, `COLOR`, and `cOloR` refer to the same property). The only exceptions are class names, [object names](http://doc.qt.io/qt-5/qobject.html#objectName-prop), and Qt property names, which are case sensitive.

Several selectors can be specified for the same declaration, using commas (`,`) to separate the selectors. For example, the rule

```
QPushButton, QLineEdit, QComboBox { color: red }

//is equivalent to this sequence of three rules:

QPushButton { color: red }
QLineEdit { color: red }
QComboBox { color: red }
```



The declaration part of a style rule is a list of `*property*: *value*` pairs, enclosed in braces (`{}`) and separated with semicolons. For example:

```
QPushButton { color: red; background-color: white }
```

### 选择器Selector Types

All the examples so far used the simplest type of selector, the Type Selector. Qt Style Sheets support all the [selectors defined in CSS2](http://www.w3.org/TR/REC-CSS2/selector.html#q1). The table below summarizes the most useful types of selectors.

| Selector            | Example                     | Explanation                                                  |
| ------------------- | --------------------------- | ------------------------------------------------------------ |
| Universal Selector  | `*`                         | Matches all widgets.                                         |
| Type Selector       | `QPushButton`               | Matches instances of [QPushButton](http://doc.qt.io/qt-5/qpushbutton.html) and of its subclasses. |
| Property Selector   | `QPushButton[flat="false"]` | Matches instances of [QPushButton](http://doc.qt.io/qt-5/qpushbutton.html) that are not [flat](http://doc.qt.io/qt-5/qpushbutton.html#flat-prop). You may use this selector to test for any Qt [property](http://doc.qt.io/qt-5/properties.html) that supports [QVariant::toString](http://doc.qt.io/qt-5/qvariant.html#toString)() (see the [toString()](http://doc.qt.io/qt-5/qvariant.html#toString) function documentation for details). In addition, the special `class` property is supported, for the name of the class.This selector may also be used to test dynamic properties. For more information on customization using dynamic properties, refer to [Customizing Using Dynamic Properties](http://doc.qt.io/qt-5/stylesheet-examples.html#customizing-using-dynamic-properties).Instead of `=`, you can also use `~=` to test whether a Qt property of type [QStringList](http://doc.qt.io/qt-5/qstringlist.html) contains a given [QString](http://doc.qt.io/qt-5/qstring.html).**Warning:** If the value of the Qt property changes after the style sheet has been set, it might be necessary to force a style sheet recomputation. One way to achieve this is to unset the style sheet and set it again. |
| Class Selector      | `.QPushButton`              | Matches instances of [QPushButton](http://doc.qt.io/qt-5/qpushbutton.html), but not of its subclasses.This is equivalent to `*[class~="QPushButton"]`. |
| ID Selector         | `QPushButton#okButton`      | Matches all [QPushButton](http://doc.qt.io/qt-5/qpushbutton.html) instances whose [object name](http://doc.qt.io/qt-5/qobject.html#objectName-prop) is `okButton`. |
| Descendant Selector | `QDialog QPushButton`       | Matches all instances of [QPushButton](http://doc.qt.io/qt-5/qpushbutton.html) that are descendants (children, grandchildren, etc.) of a [QDialog](http://doc.qt.io/qt-5/qdialog.html). |
| Child Selector      | `QDialog > QPushButton`     | Matches all instances of [QPushButton](http://doc.qt.io/qt-5/qpushbutton.html) that are direct children of a [QDialog](http://doc.qt.io/qt-5/qdialog.html). |

### 子控制Sub-Controls

For styling complex widgets, it is necessary to access subcontrols of the widget, such as the drop-down button of a [QComboBox](http://doc.qt.io/qt-5/qcombobox.html) or the up and down arrows of a [QSpinBox](http://doc.qt.io/qt-5/qspinbox.html). Selectors may contain *subcontrols* that make it possible to restrict the application of a rule to specific widget subcontrols. For example:

```
QComboBox::drop-down { image: url(dropdown.png) }
```

The above rule styles the drop-down button of all [QComboBox](http://doc.qt.io/qt-5/qcombobox.html)es. Although the double-colon (`::`) syntax is reminiscent of CSS3 Pseudo-Elements, Qt Sub-Controls differ conceptually from these and have different cascading semantics.

Sub-controls are always positioned with respect to another element - a reference element. This reference element could be the widget or another Sub-control. For example, the [::drop-down](http://doc.qt.io/qt-5/stylesheet-reference.html#drop-down-sub) of a [QComboBox](http://doc.qt.io/qt-5/qcombobox.html) is placed, by default, in the top right corner of the Padding rectangle of the [QComboBox](http://doc.qt.io/qt-5/qcombobox.html). The [::drop-down](http://doc.qt.io/qt-5/stylesheet-reference.html#drop-down-sub) is placed, by default, in the Center of the Contents rectangle of the [::drop-down](http://doc.qt.io/qt-5/stylesheet-reference.html#drop-down-sub) Sub-control. See the [List of Stylable Widgets](http://doc.qt.io/qt-5/stylesheet-reference.html#list-of-stylable-widgets) below for the Sub-controls to use to style a widget and their default positions.

The origin rectangle to be used can be changed using the [subcontrol-origin](http://doc.qt.io/qt-5/stylesheet-reference.html#subcontrol-origin-prop) property. For example, if we want to place the drop-down in the margin rectangle of the [QComboBox](http://doc.qt.io/qt-5/qcombobox.html) instead of the default Padding rectangle, we can specify:

```
QComboBox {
    margin-right: 20px;
}
QComboBox::drop-down {
    subcontrol-origin: margin;
}
```

The alignment of the drop-down within the Margin rectangle is changed using [subcontrol-position](http://doc.qt.io/qt-5/stylesheet-reference.html#subcontrol-position-prop) property.

The [width](http://doc.qt.io/qt-5/stylesheet-reference.html#width-prop) and [height](http://doc.qt.io/qt-5/stylesheet-reference.html#height-prop) properties can be used to control the size of the Sub-control. Note that setting a [image](http://doc.qt.io/qt-5/stylesheet-reference.html#image-prop) implicitly sets the size of a Sub-control.

The relative positioning scheme ([position](http://doc.qt.io/qt-5/stylesheet-reference.html#position-prop) : relative), allows the position of the Sub-Control to be offset from its initial position. For example, when the [QComboBox](http://doc.qt.io/qt-5/qcombobox.html)'s drop-down button is pressed, we might like the arrow inside to be offset to give a "pressed" effect. To achieve this, we can specify:

```
QComboBox::down-arrow {
    image: url(down_arrow.png);
}
QComboBox::down-arrow:pressed {
    position: relative;
    top: 1px; left: 1px;
}
```

The absolute positioning scheme ([position](http://doc.qt.io/qt-5/stylesheet-reference.html#position-prop) : absolute), allows the position and size of the Sub-control to be changed with respect to the reference element.

### 伪状态Pseudo-States

Selectors may contain *pseudo-states* that denote that restrict the application of the rule based on the widget's state. Pseudo-states appear at the end of the selector, with a colon (`:`) in between. For example, the following rule applies when the mouse hovers over a [QPushButton](http://doc.qt.io/qt-5/qpushbutton.html):

```
QPushButton:hover { color: white }
```

```
QRadioButton:!hover { color: red }
```

```
QCheckBox:hover:checked { color: white }
```

```
QPushButton:hover:!pressed { color: blue; }
```

```
QCheckBox:hover, QCheckBox:checked { color: white }
```

```
QComboBox::drop-down:hover { image: url(dropdown_bright.png) }
```

### 冲突处理Conflict Resolution

Conflicts arise when several style rules specify the same properties with different values. Consider the following style sheet:==冲突时保留更加具体specificity的那个样式==

```
QPushButton#okButton { color: gray }
QPushButton { color: red }
```

Both rules match [QPushButton](http://doc.qt.io/qt-5/qpushbutton.html) instances called `okButton` and there is a conflict for the `color` property. To resolve this conflict, we must take into account the *specificity* of the selectors. In the above example, `QPushButton#okButton` is considered more specific than `QPushButton`, because it (usually) refers to a single object, not to all instances of a class.

Here's a tricky one:

```
QPushButton:hover { color: white }
QPushButton:enabled { color: red }
```

对于上述两个具体程度相当的样式表来说，以下一条为准

### 级联影响Cascading

Style sheets can be set on the [QApplication](http://doc.qt.io/qt-5/qapplication.html), on parent widgets, and on child widgets. An arbitrary widget's effective style sheet is obtained by merging the style sheets set on the widget's ancestors (parent, grandparent, etc.), as well as any style sheet set on the [QApplication](http://doc.qt.io/qt-5/qapplication.html).

When conflicts arise, the widget's own style sheet is always preferred to any inherited style sheet, irrespective of the specificity of the conflicting rules. Likewise, the parent widget's style sheet is preferred to the grandparent's, etc.

One consequence of this is that setting a style rule on a widget automatically gives it precedence over other rules specified in the ancestor widgets' style sheets or the [QApplication](http://doc.qt.io/qt-5/qapplication.html) style sheet. Consider the following example. First, we set a style sheet on the [QApplication](http://doc.qt.io/qt-5/qapplication.html):

```
qApp->setStyleSheet("QPushButton { color: white }");
```

Then we set a style sheet on a [QPushButton](http://doc.qt.io/qt-5/qpushbutton.html) object:

```
myPushButton->setStyleSheet("* { color: blue }");
```

==具体的为先，子类优先==

One consequence of this is that setting a style rule on a widget automatically gives it precedence over other rules specified in the ancestor widgets' style sheets or the [QApplication](http://doc.qt.io/qt-5/qapplication.html) style sheet. Consider the following example. First, we set a style sheet on the [QApplication](http://doc.qt.io/qt-5/qapplication.html):

```
qApp->setStyleSheet("QPushButton { color: white }");
```

Then we set a style sheet on a [QPushButton](http://doc.qt.io/qt-5/qpushbutton.html) object:

```
myPushButton->setStyleSheet("* { color: blue }");
```

The style sheet on the [QPushButton](http://doc.qt.io/qt-5/qpushbutton.html) forces the [QPushButton](http://doc.qt.io/qt-5/qpushbutton.html) (and any child widget) to have blue text, in spite of the more specific rule set provided by the application-wide style sheet.

### 命名空间内 C++ Namespaces

Qt Style Sheet uses QObject::className() of the widget to determine when to apply the Type Selector. When custom widgets are inside namespaces, the QObject::className() returns <namespace>::<classname>. This conflicts with the syntax for [Sub-Controls](http://doc.qt.io/qt-5/stylesheet-syntax.html#sub-controls). To overcome this problem, when using the Type Selector for widgets inside namespaces, we must replace the "::" with "--". For example,

```
namespace ns {
    class MyPushButton : public QPushButton {
        // ...
    }
}

// ...
qApp->setStyleSheet("ns--MyPushButton { background: yellow; }");
```

### 继承Inheritance

==默认样式表不继承，可以通过特殊setStyleSheet以及开启标志的方式==

In classic CSS, when font and color of an item is not explicitly set, it gets automatically inherited from the parent. By default, when using Qt Style Sheets, a widget does **not** automatically inherit its font and color setting from its parent widget.

For example, consider a [QPushButton](http://doc.qt.io/qt-5/qpushbutton.html) inside a [QGroupBox](http://doc.qt.io/qt-5/qgroupbox.html):

```
qApp->setStyleSheet("QGroupBox { color: red; } ");
```

The [QPushButton](http://doc.qt.io/qt-5/qpushbutton.html) does not have an explicit color set. Hence, instead of inheriting color of its parent [QGroupBox](http://doc.qt.io/qt-5/qgroupbox.html), it has the system color. If we want to set the color on a [QGroupBox](http://doc.qt.io/qt-5/qgroupbox.html) and its children, we can write:

```
qApp->setStyleSheet("QGroupBox, QGroupBox * { color: red; }");
```

In contrast, setting a font and palette using [QWidget::setFont](http://doc.qt.io/qt-5/qwidget.html#font-prop)() and [QWidget::setPalette](http://doc.qt.io/qt-5/qwidget.html#palette-prop)() propagates to child widgets.

If you would prefer that the font and palette propagate to child widgets, you can set the [Qt::AA_UseStyleSheetPropagationInWidgetStyles](http://doc.qt.io/qt-5/qt.html#ApplicationAttribute-enum) flag, like this:

Usage:

```
QCoreApplication::setAttribute(Qt::AA_UseStyleSheetPropagationInWidgetStyles, true);
```

When the widget-style font and palette propagation is enabled, font and palette changes made through Qt Style Sheets will behave as though the user had manually called the corresponding [QWidget::setPalette](http://doc.qt.io/qt-5/qwidget.html#palette-prop)() and [QWidget::setFont](http://doc.qt.io/qt-5/qwidget.html#font-prop)() methods on all of the QWidgets targeted by the style sheet. If this would have caused propagation in C++, it will cause propagation in style sheets and visa versa.

### Setting QObject Properties

*通过样式表设置QObject的属性值*

From 4.3 and above, any designable [Q_PROPERTY](http://doc.qt.io/qt-5/qobject.html#Q_PROPERTY) can be set using the qproperty-<property name> syntax.

```
MyLabel { qproperty-pixmap: url(pixmap.png); }
MyGroupBox { qproperty-titleColor: rgb(100, 200, 100); }
QPushButton { qproperty-iconSize: 20px 20px; }
```



### 样式表内的属性

| Property                                        |                             Type                             | DescriptionDescription                                       |
| :---------------------------------------------- | :----------------------------------------------------------: | ------------------------------------------------------------ |
| **alternate-background-color**                  | [Brush](http://doc.qt.io/qt-5/stylesheet-reference.html#brush) | The [alternate background color](http://doc.qt.io/qt-5/qabstractitemview.html#alternatingRowColors-prop) used in [QAbstractItemView](http://doc.qt.io/qt-5/qabstractitemview.html) subclasses.If this property is not set, the default value is whatever is set for the palette's [AlternateBase](http://doc.qt.io/qt-5/qpalette.html#ColorRole-enum) role.Example:`QTreeView {    alternate-background-color: blue;    background: yellow;}`See also [background](http://doc.qt.io/qt-5/stylesheet-reference.html#background-prop) and [selection-background-color](http://doc.qt.io/qt-5/stylesheet-reference.html#selection-background-color-prop). |
| **background**                                  | [Background](http://doc.qt.io/qt-5/stylesheet-reference.html#background) | Shorthand notation for setting the background. Equivalent to specifying `background-color`, `background-image`, `background-repeat`, and/or `background-position`.This property is supported by [QAbstractItemView](http://doc.qt.io/qt-5/qabstractitemview.html) subclasses, [QAbstractSpinBox](http://doc.qt.io/qt-5/qabstractspinbox.html)subclasses, [QCheckBox](http://doc.qt.io/qt-5/qcheckbox.html), [QComboBox](http://doc.qt.io/qt-5/qcombobox.html), [QDialog](http://doc.qt.io/qt-5/qdialog.html), [QFrame](http://doc.qt.io/qt-5/qframe.html), [QGroupBox](http://doc.qt.io/qt-5/qgroupbox.html), [QLabel](http://doc.qt.io/qt-5/qlabel.html), [QLineEdit](http://doc.qt.io/qt-5/qlineedit.html), [QMenu](http://doc.qt.io/qt-5/qmenu.html), [QMenuBar](http://doc.qt.io/qt-5/qmenubar.html), [QPushButton](http://doc.qt.io/qt-5/qpushbutton.html), [QRadioButton](http://doc.qt.io/qt-5/qradiobutton.html), [QSplitter](http://doc.qt.io/qt-5/qsplitter.html), [QTextEdit](http://doc.qt.io/qt-5/qtextedit.html), [QToolTip](http://doc.qt.io/qt-5/qtooltip.html), and plain [QWidget](http://doc.qt.io/qt-5/qwidget.html)s.Example:`QTextEdit { background: yellow }`Often, it is required to set a fill pattern similar to the styles in [Qt::BrushStyle](http://doc.qt.io/qt-5/qt.html#BrushStyle-enum). You can use the background-color property for [Qt::SolidPattern](http://doc.qt.io/qt-5/qt.html#BrushStyle-enum), [Qt::RadialGradientPattern](http://doc.qt.io/qt-5/qt.html#BrushStyle-enum), [Qt::LinearGradientPattern](http://doc.qt.io/qt-5/qt.html#BrushStyle-enum) and [Qt::ConicalGradientPattern](http://doc.qt.io/qt-5/qt.html#BrushStyle-enum). The other patterns are easily achieved by creating a background image that contains the pattern.Example:`QLabel {    background-image: url(dense6pattern.png);    background-repeat: repeat-xy;}`See also [background-origin](http://doc.qt.io/qt-5/stylesheet-reference.html#background-origin-prop), [selection-background-color](http://doc.qt.io/qt-5/stylesheet-reference.html#selection-background-color-prop), [background-clip](http://doc.qt.io/qt-5/stylesheet-reference.html#background-clip-prop), [background-attachment](http://doc.qt.io/qt-5/stylesheet-reference.html#background-attachment-prop) and [alternate-background-color](http://doc.qt.io/qt-5/stylesheet-reference.html#alternate-background-color-prop). |
| `background-color`                              | [Brush](http://doc.qt.io/qt-5/stylesheet-reference.html#brush) | The background color used for the widget.Examples:`QLabel { background-color: yellow }QLineEdit { background-color: rgb(255, 0, 0) }` |
| `background-image`                              |  [Url](http://doc.qt.io/qt-5/stylesheet-reference.html#url)  | The background image used for the widget. Semi-transparent parts of the image let the `background-color` shine through.Example:`QFrame { background-image: url(:/images/hydro.png) }` |
| `background-repeat`                             | [Repeat](http://doc.qt.io/qt-5/stylesheet-reference.html#repeat) | Whether and how the background image is repeated to fill the `background-origin`rectangle.If this property is not specified, the background image is repeated in both directions (`repeat`).Example:`QFrame {    background: white url(:/images/ring.png);    background-repeat: repeat-y;    background-position: left;}` |
| `background-position`                           | [Alignment](http://doc.qt.io/qt-5/stylesheet-reference.html#alignment) | The alignment of the background image within the `background-origin` rectangle.If this property is not specified, the alignment is `top` `left`.Example:`QFrame {    background: url(:/images/footer.png);    background-position: bottom left;}` |
| **background-attachment**                       | [Attachment](http://doc.qt.io/qt-5/stylesheet-reference.html#attachment) | Determines whether the background-image in a [QAbstractScrollArea](http://doc.qt.io/qt-5/qabstractscrollarea.html) is scrolled or fixed with respect to the viewport. By default, the background-image scrolls with the viewport.Example:`QTextEdit {    background-image: url("leaves.png");    background-attachment: fixed;}`See also [background](http://doc.qt.io/qt-5/stylesheet-reference.html#background-prop) |
| **background-clip**                             | [Origin](http://doc.qt.io/qt-5/stylesheet-reference.html#origin) | The widget's rectangle, in which the `background` is drawn.This property specifies the rectangle to which the `background-color` and `background-image` are clipped.This property is supported by [QAbstractItemView](http://doc.qt.io/qt-5/qabstractitemview.html) subclasses, [QAbstractSpinBox](http://doc.qt.io/qt-5/qabstractspinbox.html)subclasses, [QCheckBox](http://doc.qt.io/qt-5/qcheckbox.html), [QComboBox](http://doc.qt.io/qt-5/qcombobox.html), [QDialog](http://doc.qt.io/qt-5/qdialog.html), [QFrame](http://doc.qt.io/qt-5/qframe.html), [QGroupBox](http://doc.qt.io/qt-5/qgroupbox.html), [QLabel](http://doc.qt.io/qt-5/qlabel.html), [QPushButton](http://doc.qt.io/qt-5/qpushbutton.html), [QRadioButton](http://doc.qt.io/qt-5/qradiobutton.html), [QSplitter](http://doc.qt.io/qt-5/qsplitter.html), [QTextEdit](http://doc.qt.io/qt-5/qtextedit.html), [QToolTip](http://doc.qt.io/qt-5/qtooltip.html), and plain [QWidget](http://doc.qt.io/qt-5/qwidget.html)s.If this property is not specified, the default is `border`.Example:`QFrame {    background-image: url(:/images/header.png);    background-position: top left;    background-origin: content;    background-clip: padding;}`See also [background](http://doc.qt.io/qt-5/stylesheet-reference.html#background-prop), [background-origin](http://doc.qt.io/qt-5/stylesheet-reference.html#background-origin-prop) and [The Box Model](http://doc.qt.io/qt-5/stylesheet-customizing.html#the-box-model). |
| **background-origin**                           | [Origin](http://doc.qt.io/qt-5/stylesheet-reference.html#origin) | The widget's background rectangle, to use in conjunction with `background-position` and `background-image`.This property is supported by [QAbstractItemView](http://doc.qt.io/qt-5/qabstractitemview.html) subclasses, [QAbstractSpinBox](http://doc.qt.io/qt-5/qabstractspinbox.html)subclasses, [QCheckBox](http://doc.qt.io/qt-5/qcheckbox.html), [QComboBox](http://doc.qt.io/qt-5/qcombobox.html), [QDialog](http://doc.qt.io/qt-5/qdialog.html), [QFrame](http://doc.qt.io/qt-5/qframe.html), [QGroupBox](http://doc.qt.io/qt-5/qgroupbox.html), [QLabel](http://doc.qt.io/qt-5/qlabel.html), [QPushButton](http://doc.qt.io/qt-5/qpushbutton.html), [QRadioButton](http://doc.qt.io/qt-5/qradiobutton.html), [QSplitter](http://doc.qt.io/qt-5/qsplitter.html), [QTextEdit](http://doc.qt.io/qt-5/qtextedit.html), [QToolTip](http://doc.qt.io/qt-5/qtooltip.html), and plain [QWidget](http://doc.qt.io/qt-5/qwidget.html)s.If this property is not specified, the default is `padding`.Example:`QFrame {    background-image: url(:/images/header.png);    background-position: top left;    background-origin: content;}`See also [background](http://doc.qt.io/qt-5/stylesheet-reference.html#background-prop) and [The Box Model](http://doc.qt.io/qt-5/stylesheet-customizing.html#the-box-model). |
| **border**                                      | [Border](http://doc.qt.io/qt-5/stylesheet-reference.html#border) | Shorthand notation for setting the widget's border. Equivalent to specifying `border-color`, `border-style`, and/or `border-width`.This property is supported by [QAbstractItemView](http://doc.qt.io/qt-5/qabstractitemview.html) subclasses, [QAbstractSpinBox](http://doc.qt.io/qt-5/qabstractspinbox.html)subclasses, [QCheckBox](http://doc.qt.io/qt-5/qcheckbox.html), [QComboBox](http://doc.qt.io/qt-5/qcombobox.html), [QFrame](http://doc.qt.io/qt-5/qframe.html), [QGroupBox](http://doc.qt.io/qt-5/qgroupbox.html), [QLabel](http://doc.qt.io/qt-5/qlabel.html), [QLineEdit](http://doc.qt.io/qt-5/qlineedit.html), [QMenu](http://doc.qt.io/qt-5/qmenu.html), [QMenuBar](http://doc.qt.io/qt-5/qmenubar.html), [QPushButton](http://doc.qt.io/qt-5/qpushbutton.html), [QRadioButton](http://doc.qt.io/qt-5/qradiobutton.html), [QSplitter](http://doc.qt.io/qt-5/qsplitter.html), [QTextEdit](http://doc.qt.io/qt-5/qtextedit.html), [QToolTip](http://doc.qt.io/qt-5/qtooltip.html), and plain [QWidget](http://doc.qt.io/qt-5/qwidget.html)s.Example:`QLineEdit { border: 1px solid white }` |
| `border-top`                                    | [Border](http://doc.qt.io/qt-5/stylesheet-reference.html#border) | Shorthand notation for setting the widget's top border. Equivalent to specifying `border-top-color`, `border-top-style`, and/or `border-top-width`. |
| `border-right`                                  | [Border](http://doc.qt.io/qt-5/stylesheet-reference.html#border) | Shorthand notation for setting the widget's right border. Equivalent to specifying `border-right-color`, `border-right-style`, and/or `border-right-width`. |
| `border-bottom`                                 | [Border](http://doc.qt.io/qt-5/stylesheet-reference.html#border) | Shorthand notation for setting the widget's bottom border. Equivalent to specifying `border-bottom-color`, `border-bottom-style`, and/or `border-bottom-width`. |
| `border-left`                                   | [Border](http://doc.qt.io/qt-5/stylesheet-reference.html#border) | Shorthand notation for setting the widget's left border. Equivalent to specifying `border-left-color`, `border-left-style`, and/or `border-left-width`. |
| **border-color**                                | [Box Colors](http://doc.qt.io/qt-5/stylesheet-reference.html#box-colors) | The color of all the border's edges. Equivalent to specifying `border-top-color`, `border-right-color`, `border-bottom-color`, and `border-left-color`.This property is supported by [QAbstractItemView](http://doc.qt.io/qt-5/qabstractitemview.html) subclasses, [QAbstractSpinBox](http://doc.qt.io/qt-5/qabstractspinbox.html)subclasses, [QCheckBox](http://doc.qt.io/qt-5/qcheckbox.html), [QComboBox](http://doc.qt.io/qt-5/qcombobox.html), [QFrame](http://doc.qt.io/qt-5/qframe.html), [QGroupBox](http://doc.qt.io/qt-5/qgroupbox.html), [QLabel](http://doc.qt.io/qt-5/qlabel.html), [QLineEdit](http://doc.qt.io/qt-5/qlineedit.html), [QMenu](http://doc.qt.io/qt-5/qmenu.html), [QMenuBar](http://doc.qt.io/qt-5/qmenubar.html), [QPushButton](http://doc.qt.io/qt-5/qpushbutton.html), [QRadioButton](http://doc.qt.io/qt-5/qradiobutton.html), [QSplitter](http://doc.qt.io/qt-5/qsplitter.html), [QTextEdit](http://doc.qt.io/qt-5/qtextedit.html), [QToolTip](http://doc.qt.io/qt-5/qtooltip.html), and plain [QWidget](http://doc.qt.io/qt-5/qwidget.html)s.If this property is not specified, it defaults to [color](http://doc.qt.io/qt-5/stylesheet-reference.html#color-prop) (i.e., the widget's foreground color).Example:`QLineEdit {    border-width: 1px;    border-style: solid;    border-color: white;}`See also [border-style](http://doc.qt.io/qt-5/stylesheet-reference.html#border-style-prop), [border-width](http://doc.qt.io/qt-5/stylesheet-reference.html#border-width-prop), [border-image](http://doc.qt.io/qt-5/stylesheet-reference.html#border-image-prop), and [The Box Model](http://doc.qt.io/qt-5/stylesheet-customizing.html#the-box-model). |
| `border-top-color`                              | [Brush](http://doc.qt.io/qt-5/stylesheet-reference.html#brush) | The color of the border's top edge.                          |
| `border-right-color`                            | [Brush](http://doc.qt.io/qt-5/stylesheet-reference.html#brush) | The color of the border's right edge.                        |
| `border-bottom-color`                           | [Brush](http://doc.qt.io/qt-5/stylesheet-reference.html#brush) | The color of the border's bottom edge.                       |
| `border-left-color`                             | [Brush](http://doc.qt.io/qt-5/stylesheet-reference.html#brush) | The color of the border's left edge.                         |
| **border-image**                                | [Border Image](http://doc.qt.io/qt-5/stylesheet-reference.html#border-image) | The image used to fill the border. The image is cut into nine parts and stretched appropriately if necessary. See [Border Image](http://doc.qt.io/qt-5/stylesheet-reference.html#border-image) for details.This property is supported by [QAbstractItemView](http://doc.qt.io/qt-5/qabstractitemview.html) subclasses, [QAbstractSpinBox](http://doc.qt.io/qt-5/qabstractspinbox.html)subclasses, [QCheckBox](http://doc.qt.io/qt-5/qcheckbox.html), [QComboBox](http://doc.qt.io/qt-5/qcombobox.html), [QFrame](http://doc.qt.io/qt-5/qframe.html), [QGroupBox](http://doc.qt.io/qt-5/qgroupbox.html), [QLabel](http://doc.qt.io/qt-5/qlabel.html), [QLineEdit](http://doc.qt.io/qt-5/qlineedit.html), [QMenu](http://doc.qt.io/qt-5/qmenu.html), [QMenuBar](http://doc.qt.io/qt-5/qmenubar.html), [QPushButton](http://doc.qt.io/qt-5/qpushbutton.html), [QRadioButton](http://doc.qt.io/qt-5/qradiobutton.html), [QSplitter](http://doc.qt.io/qt-5/qsplitter.html), [QTextEdit](http://doc.qt.io/qt-5/qtextedit.html) and [QToolTip](http://doc.qt.io/qt-5/qtooltip.html).See also [border-color](http://doc.qt.io/qt-5/stylesheet-reference.html#border-color-prop), [border-style](http://doc.qt.io/qt-5/stylesheet-reference.html#border-style-prop), [border-width](http://doc.qt.io/qt-5/stylesheet-reference.html#border-width-prop), and [The Box Model](http://doc.qt.io/qt-5/stylesheet-customizing.html#the-box-model). |
| **border-radius**                               | [Radius](http://doc.qt.io/qt-5/stylesheet-reference.html#radius) | The radius of the border's corners. Equivalent to specifying `border-top-left-radius`, `border-top-right-radius`, `border-bottom-right-radius`, and `border-bottom-left-radius`.The border-radius clips the element's [background](http://doc.qt.io/qt-5/stylesheet-reference.html#background-prop).This property is supported by [QAbstractItemView](http://doc.qt.io/qt-5/qabstractitemview.html) subclasses, [QAbstractSpinBox](http://doc.qt.io/qt-5/qabstractspinbox.html)subclasses, [QCheckBox](http://doc.qt.io/qt-5/qcheckbox.html), [QComboBox](http://doc.qt.io/qt-5/qcombobox.html), [QFrame](http://doc.qt.io/qt-5/qframe.html), [QGroupBox](http://doc.qt.io/qt-5/qgroupbox.html), [QLabel](http://doc.qt.io/qt-5/qlabel.html), [QLineEdit](http://doc.qt.io/qt-5/qlineedit.html), [QMenu](http://doc.qt.io/qt-5/qmenu.html), [QMenuBar](http://doc.qt.io/qt-5/qmenubar.html), [QPushButton](http://doc.qt.io/qt-5/qpushbutton.html), [QRadioButton](http://doc.qt.io/qt-5/qradiobutton.html), [QSplitter](http://doc.qt.io/qt-5/qsplitter.html), [QTextEdit](http://doc.qt.io/qt-5/qtextedit.html), and [QToolTip](http://doc.qt.io/qt-5/qtooltip.html).If this property is not specified, it defaults to 0.Example:`QLineEdit {    border-width: 1px;    border-style: solid;    border-radius: 4px;}`See also [border-width](http://doc.qt.io/qt-5/stylesheet-reference.html#border-width-prop) and [The Box Model](http://doc.qt.io/qt-5/stylesheet-customizing.html#the-box-model). |
| `border-top-left-radius`                        | [Radius](http://doc.qt.io/qt-5/stylesheet-reference.html#radius) | The radius of the border's top-left corner.                  |
| `border-top-right-radius`                       | [Radius](http://doc.qt.io/qt-5/stylesheet-reference.html#radius) | The radius of the border's top-right corner.                 |
| `border-bottom-right-radius`                    | [Radius](http://doc.qt.io/qt-5/stylesheet-reference.html#radius) | The radius of the border's bottom-right corner. Setting this property to a positive value results in a rounded corner. |
| `border-bottom-left-radius`                     | [Radius](http://doc.qt.io/qt-5/stylesheet-reference.html#radius) | The radius of the border's bottom-left corner. Setting this property to a positive value results in a rounded corner. |
| **border-style**                                | [Border Style](http://doc.qt.io/qt-5/stylesheet-reference.html#border-style) | The style of all the border's edges.This property is supported by [QAbstractItemView](http://doc.qt.io/qt-5/qabstractitemview.html) subclasses, [QAbstractSpinBox](http://doc.qt.io/qt-5/qabstractspinbox.html)subclasses, [QCheckBox](http://doc.qt.io/qt-5/qcheckbox.html), [QComboBox](http://doc.qt.io/qt-5/qcombobox.html), [QFrame](http://doc.qt.io/qt-5/qframe.html), [QGroupBox](http://doc.qt.io/qt-5/qgroupbox.html), [QLabel](http://doc.qt.io/qt-5/qlabel.html), [QLineEdit](http://doc.qt.io/qt-5/qlineedit.html), [QMenu](http://doc.qt.io/qt-5/qmenu.html), [QMenuBar](http://doc.qt.io/qt-5/qmenubar.html), [QPushButton](http://doc.qt.io/qt-5/qpushbutton.html), [QRadioButton](http://doc.qt.io/qt-5/qradiobutton.html), [QSplitter](http://doc.qt.io/qt-5/qsplitter.html), [QTextEdit](http://doc.qt.io/qt-5/qtextedit.html), and [QToolTip](http://doc.qt.io/qt-5/qtooltip.html).If this property is not specified, it defaults to `none`.Example:`QLineEdit {    border-width: 1px;    border-style: solid;    border-color: blue;}`See also [border-color](http://doc.qt.io/qt-5/stylesheet-reference.html#border-color-prop), [border-style](http://doc.qt.io/qt-5/stylesheet-reference.html#border-style-prop), [border-image](http://doc.qt.io/qt-5/stylesheet-reference.html#border-image-prop), and [The Box Model](http://doc.qt.io/qt-5/stylesheet-customizing.html#the-box-model). |
| `border-top-style`                              | [Border Style](http://doc.qt.io/qt-5/stylesheet-reference.html#border-style) | The style of the border's top edge.                          |
| `border-right-style`                            | [Border Style](http://doc.qt.io/qt-5/stylesheet-reference.html#border-style) | The style of the border's right edge/                        |
| `border-bottom-style`                           | [Border Style](http://doc.qt.io/qt-5/stylesheet-reference.html#border-style) | The style of the border's bottom edge.                       |
| `border-left-style`                             | [Border Style](http://doc.qt.io/qt-5/stylesheet-reference.html#border-style) | The style of the border's left edge.                         |
| **border-width**                                | [Box Lengths](http://doc.qt.io/qt-5/stylesheet-reference.html#box-lengths) | The width of the border. Equivalent to setting `border-top-width`, `border-right-width`, `border-bottom-width`, and `border-left-width`.This property is supported by [QAbstractItemView](http://doc.qt.io/qt-5/qabstractitemview.html) subclasses, [QAbstractSpinBox](http://doc.qt.io/qt-5/qabstractspinbox.html)subclasses, [QCheckBox](http://doc.qt.io/qt-5/qcheckbox.html), [QComboBox](http://doc.qt.io/qt-5/qcombobox.html), [QFrame](http://doc.qt.io/qt-5/qframe.html), [QGroupBox](http://doc.qt.io/qt-5/qgroupbox.html), [QLabel](http://doc.qt.io/qt-5/qlabel.html), [QLineEdit](http://doc.qt.io/qt-5/qlineedit.html), [QMenu](http://doc.qt.io/qt-5/qmenu.html), [QMenuBar](http://doc.qt.io/qt-5/qmenubar.html), [QPushButton](http://doc.qt.io/qt-5/qpushbutton.html), [QRadioButton](http://doc.qt.io/qt-5/qradiobutton.html), [QSplitter](http://doc.qt.io/qt-5/qsplitter.html), [QTextEdit](http://doc.qt.io/qt-5/qtextedit.html), and [QToolTip](http://doc.qt.io/qt-5/qtooltip.html).Example:`QLineEdit {    border-width: 2px;    border-style: solid;    border-color: darkblue;}`See also [border-color](http://doc.qt.io/qt-5/stylesheet-reference.html#border-color-prop), [border-radius](http://doc.qt.io/qt-5/stylesheet-reference.html#border-radius-prop), [border-style](http://doc.qt.io/qt-5/stylesheet-reference.html#border-style-prop), [border-image](http://doc.qt.io/qt-5/stylesheet-reference.html#border-image-prop), and [The Box Model](http://doc.qt.io/qt-5/stylesheet-customizing.html#the-box-model). |
| `border-top-width`                              | [Length](http://doc.qt.io/qt-5/stylesheet-reference.html#length) | The width of the border's top edge.                          |
| `border-right-width`                            | [Length](http://doc.qt.io/qt-5/stylesheet-reference.html#length) | The width of the border's right edge.                        |
| `border-bottom-width`                           | [Length](http://doc.qt.io/qt-5/stylesheet-reference.html#length) | The width of the border's bottom edge.                       |
| `border-left-width`                             | [Length](http://doc.qt.io/qt-5/stylesheet-reference.html#length) | The width of the border's left edge.                         |
| **bottom**                                      | [Length](http://doc.qt.io/qt-5/stylesheet-reference.html#length) | If [position](http://doc.qt.io/qt-5/stylesheet-reference.html#position-prop) is `relative` (the default), moves a subcontrol by a certain offset up; specifying `bottom: *y*` is then equivalent to specifying `top: -*y*`.If [position](http://doc.qt.io/qt-5/stylesheet-reference.html#position-prop) is `absolute`, the `bottom` property specifies the subcontrol's bottom edge in relation to the parent's bottom edge (see also [subcontrol-origin](http://doc.qt.io/qt-5/stylesheet-reference.html#subcontrol-origin-prop)).Example:`QSpinBox::down-button { bottom: 2px }`See also [left](http://doc.qt.io/qt-5/stylesheet-reference.html#left-prop), [right](http://doc.qt.io/qt-5/stylesheet-reference.html#right-prop), and [top](http://doc.qt.io/qt-5/stylesheet-reference.html#top-prop). |
| **button-layout**                               | [Number](http://doc.qt.io/qt-5/stylesheet-reference.html#number) | The layout of buttons in a [QDialogButtonBox](http://doc.qt.io/qt-5/qdialogbuttonbox.html) or a [QMessageBox](http://doc.qt.io/qt-5/qmessagebox.html). The possible values are 0 ([WinLayout](http://doc.qt.io/qt-5/qdialogbuttonbox.html#ButtonLayout-enum)), 1 ([MacLayout](http://doc.qt.io/qt-5/qdialogbuttonbox.html#ButtonLayout-enum)), 2 ([KdeLayout](http://doc.qt.io/qt-5/qdialogbuttonbox.html#ButtonLayout-enum)), 3 ([GnomeLayout](http://doc.qt.io/qt-5/qdialogbuttonbox.html#ButtonLayout-enum)) and 5 ([AndroidLayout](http://doc.qt.io/qt-5/qdialogbuttonbox.html#ButtonLayout-enum)).If this property is not specified, it defaults to the value specified by the current style for the [SH_DialogButtonLayout](http://doc.qt.io/qt-5/qstyle.html#StyleHint-enum) style hint.Example:`* { button-layout: 2 }` |
| **color**                                       | [Brush](http://doc.qt.io/qt-5/stylesheet-reference.html#brush) | The color used to render text.This property is supported by all widgets that respect the [QWidget::palette](http://doc.qt.io/qt-5/qwidget.html#palette-prop).If this property is not set, the default is whatever is set for in the widget's palette for the [QWidget::foregroundRole](http://doc.qt.io/qt-5/qwidget.html#foregroundRole) (typically black).Example:`QPushButton { color: red }`See also [background](http://doc.qt.io/qt-5/stylesheet-reference.html#background-prop) and [selection-color](http://doc.qt.io/qt-5/stylesheet-reference.html#selection-color-prop). |
| **dialogbuttonbox-buttons-have-icons**          | [Boolean](http://doc.qt.io/qt-5/stylesheet-reference.html#boolean) | Whether the buttons in a [QDialogButtonBox](http://doc.qt.io/qt-5/qdialogbuttonbox.html) show iconsIf this property is set to 1, the buttons of a [QDialogButtonBox](http://doc.qt.io/qt-5/qdialogbuttonbox.html) show icons; if it is set to 0, the icons are not shown.See the [List of Icons](http://doc.qt.io/qt-5/stylesheet-reference.html#list-of-icons) section for information on how to set icons.`QDialogButtonBox { dialogbuttonbox-buttons-have-icons: 1; }`**Note:** Styles defining this property must be applied before the [QDialogButtonBox](http://doc.qt.io/qt-5/qdialogbuttonbox.html) is created; this means that you must apply the style to the parent widget or to the application itself. |
| **font**                                        | [Font](http://doc.qt.io/qt-5/stylesheet-reference.html#font) | Shorthand notation for setting the text's font. Equivalent to specifying `font-family`, `font-size`, `font-style`, and/or `font-weight`.This property is supported by all widgets that respect the [QWidget::font](http://doc.qt.io/qt-5/qwidget.html#font-prop).If this property is not set, the default is the [QWidget::font](http://doc.qt.io/qt-5/qwidget.html#font-prop).Example:`QCheckBox { font: bold italic large "Times New Roman" }` |
| `font-family`                                   |                            String                            | The font family.Example:`QCheckBox { font-family: "New Century Schoolbook" }` |
| `font-size`                                     | [Font Size](http://doc.qt.io/qt-5/stylesheet-reference.html#font-size) | The font size. In this version of Qt, only pt and px metrics are supported.Example:`QTextEdit { font-size: 12px }` |
| `font-style`                                    | [Font Style](http://doc.qt.io/qt-5/stylesheet-reference.html#font-style) | The font style.Example:`QTextEdit { font-style: italic }`    |
| `font-weight`                                   | [Font Weight](http://doc.qt.io/qt-5/stylesheet-reference.html#font-weight) | The weight of the font.                                      |
| **gridline-color***                             | [Color](http://doc.qt.io/qt-5/stylesheet-reference.html#color) | The color of the grid line in a [QTableView](http://doc.qt.io/qt-5/qtableview.html).If this property is not specified, it defaults to the value specified by the current style for the [SH_Table_GridLineColor](http://doc.qt.io/qt-5/qstyle.html#StyleHint-enum) style hint.Example:`* { gridline-color: gray }` |
| **height**                                      | [Length](http://doc.qt.io/qt-5/stylesheet-reference.html#length) | The height of a subcontrol (or in some case, a widget).If this property is not specified, it defaults to a value that depends on the subcontrol/widget and on the current style.**Warning:** Unless otherwise specified, this property has no effect when set on widgets. If you want a widget with a fixed height, set the [min-height](http://doc.qt.io/qt-5/stylesheet-reference.html#min-width-prop) and [max-height](http://doc.qt.io/qt-5/stylesheet-reference.html#max-width-prop) to the same value.Example:`QSpinBox::down-button { height: 10px }`See also [width](http://doc.qt.io/qt-5/stylesheet-reference.html#width-prop). |
| **icon-size**                                   | [Length](http://doc.qt.io/qt-5/stylesheet-reference.html#length) | The width and height of the icon in a widget.The icon size of the following widgets can be set using this property.[QCheckBox](http://doc.qt.io/qt-5/qcheckbox.html)[QListView](http://doc.qt.io/qt-5/qlistview.html)[QPushButton](http://doc.qt.io/qt-5/qpushbutton.html)[QRadioButton](http://doc.qt.io/qt-5/qradiobutton.html)[QTabBar](http://doc.qt.io/qt-5/qtabbar.html)[QToolBar](http://doc.qt.io/qt-5/qtoolbar.html)[QToolBox](http://doc.qt.io/qt-5/qtoolbox.html)[QTreeView](http://doc.qt.io/qt-5/qtreeview.html) |
| **image***                                      | [Url](http://doc.qt.io/qt-5/stylesheet-reference.html#url)+  | The image that is drawn in the contents rectangle of a subcontrol.The image property accepts a list of [Url](http://doc.qt.io/qt-5/stylesheet-reference.html#url)s or an `svg`. The actual image that is drawn is determined using the same algorithm as [QIcon](http://doc.qt.io/qt-5/qicon.html) (i.e) the image is never scaled up but always scaled down if necessary. If a `svg` is specified, the image is scaled to the size of the contents rectangle.Setting the image property on sub controls implicitly sets the width and height of the sub-control (unless the image in a SVG).In Qt 4.3 and later, the alignment of the image within the rectangle can be specified using [image-position](http://doc.qt.io/qt-5/stylesheet-reference.html#image-position-prop).This property is for subcontrols only--we don't support it for other elements.**Warning:** The [QIcon](http://doc.qt.io/qt-5/qicon.html) SVG plugin is needed to render SVG images.Example:`// implicitly sets the size of down-button to the// size of spindown.pngQSpinBox::down-button { image: url(:/images/spindown.png) }` |
| **image-position**                              | [alignment](http://doc.qt.io/qt-5/stylesheet-reference.html#alignment) | In Qt 4.3 and later, the alignment of the image image's position can be specified using relative or absolute position. |
| **left**                                        | [Length](http://doc.qt.io/qt-5/stylesheet-reference.html#length) | If [position](http://doc.qt.io/qt-5/stylesheet-reference.html#position-prop) is `relative` (the default), moves a subcontrol by a certain offset to the right.If [position](http://doc.qt.io/qt-5/stylesheet-reference.html#position-prop) is `absolute`, the `left` property specifies the subcontrol's left edge in relation to the parent's left edge (see also [subcontrol-origin](http://doc.qt.io/qt-5/stylesheet-reference.html#subcontrol-origin-prop)).If this property is not specified, it defaults to `0`.Example:`QSpinBox::down-button { left: 2px }`See also [right](http://doc.qt.io/qt-5/stylesheet-reference.html#right-prop), [top](http://doc.qt.io/qt-5/stylesheet-reference.html#top-prop), and [bottom](http://doc.qt.io/qt-5/stylesheet-reference.html#bottom-prop). |
| **lineedit-password-character\***               | [Number](http://doc.qt.io/qt-5/stylesheet-reference.html#number) | The [QLineEdit](http://doc.qt.io/qt-5/qlineedit.html) password character as a Unicode number.If this property is not specified, it defaults to the value specified by the current style for the [SH_LineEdit_PasswordCharacter](http://doc.qt.io/qt-5/qstyle.html#StyleHint-enum) style hint.Example:`* { lineedit-password-character: 9679 }` |
| **lineedit-password-mask-delay\***              | [Number](http://doc.qt.io/qt-5/stylesheet-reference.html#number) | The [QLineEdit](http://doc.qt.io/qt-5/qlineedit.html) password mask delay in milliseconds before [lineedit-password-character](http://doc.qt.io/qt-5/stylesheet-reference.html#lineedit-password-character-prop)is applied to visible character.If this property is not specified, it defaults to the value specified by the current style for the [SH_LineEdit_PasswordMaskDelay](http://doc.qt.io/qt-5/qstyle.html#StyleHint-enum) style hint.**This property was added in Qt 5.4.**Example:`* { lineedit-password-mask-delay: 1000 }` |
| **margin边缘**                                  | [Box Lengths](http://doc.qt.io/qt-5/stylesheet-reference.html#box-lengths) | The widget's margins. Equivalent to specifying `margin-top`, `margin-right`, `margin-bottom`, and `margin-left`.This property is supported by [QAbstractItemView](http://doc.qt.io/qt-5/qabstractitemview.html) subclasses, [QAbstractSpinBox](http://doc.qt.io/qt-5/qabstractspinbox.html)subclasses, [QCheckBox](http://doc.qt.io/qt-5/qcheckbox.html), [QComboBox](http://doc.qt.io/qt-5/qcombobox.html), [QFrame](http://doc.qt.io/qt-5/qframe.html), [QGroupBox](http://doc.qt.io/qt-5/qgroupbox.html), [QLabel](http://doc.qt.io/qt-5/qlabel.html), [QLineEdit](http://doc.qt.io/qt-5/qlineedit.html), [QMenu](http://doc.qt.io/qt-5/qmenu.html), [QMenuBar](http://doc.qt.io/qt-5/qmenubar.html), [QPushButton](http://doc.qt.io/qt-5/qpushbutton.html), [QRadioButton](http://doc.qt.io/qt-5/qradiobutton.html), [QSplitter](http://doc.qt.io/qt-5/qsplitter.html), [QTextEdit](http://doc.qt.io/qt-5/qtextedit.html), and [QToolTip](http://doc.qt.io/qt-5/qtooltip.html).If this property is not specified, it defaults to `0`.Example:`QLineEdit { margin: 2px }`See also [padding](http://doc.qt.io/qt-5/stylesheet-reference.html#padding-prop), [spacing](http://doc.qt.io/qt-5/stylesheet-reference.html#spacing-prop), and [The Box Model](http://doc.qt.io/qt-5/stylesheet-customizing.html#the-box-model). |
| `margin-top`                                    | [Length](http://doc.qt.io/qt-5/stylesheet-reference.html#length) | The widget's top margin.                                     |
| `margin-right`                                  | [Length](http://doc.qt.io/qt-5/stylesheet-reference.html#length) | The widget's right margin.                                   |
| `margin-bottom`                                 | [Length](http://doc.qt.io/qt-5/stylesheet-reference.html#length) | The widget's bottom margin.                                  |
| `margin-left`                                   | [Length](http://doc.qt.io/qt-5/stylesheet-reference.html#length) | The widget's left margin.                                    |
| **max-height**                                  | [Length](http://doc.qt.io/qt-5/stylesheet-reference.html#length) | The widget's or a subcontrol's maximum height.This property is supported by [QAbstractItemView](http://doc.qt.io/qt-5/qabstractitemview.html) subclasses, [QAbstractSpinBox](http://doc.qt.io/qt-5/qabstractspinbox.html)subclasses, [QCheckBox](http://doc.qt.io/qt-5/qcheckbox.html), [QComboBox](http://doc.qt.io/qt-5/qcombobox.html), [QFrame](http://doc.qt.io/qt-5/qframe.html), [QGroupBox](http://doc.qt.io/qt-5/qgroupbox.html), [QLabel](http://doc.qt.io/qt-5/qlabel.html), [QLineEdit](http://doc.qt.io/qt-5/qlineedit.html), [QMenu](http://doc.qt.io/qt-5/qmenu.html), [QMenuBar](http://doc.qt.io/qt-5/qmenubar.html), [QPushButton](http://doc.qt.io/qt-5/qpushbutton.html), [QRadioButton](http://doc.qt.io/qt-5/qradiobutton.html), [QSizeGrip](http://doc.qt.io/qt-5/qsizegrip.html), [QSpinBox](http://doc.qt.io/qt-5/qspinbox.html), [QSplitter](http://doc.qt.io/qt-5/qsplitter.html), [QStatusBar](http://doc.qt.io/qt-5/qstatusbar.html), [QTextEdit](http://doc.qt.io/qt-5/qtextedit.html), and [QToolTip](http://doc.qt.io/qt-5/qtooltip.html).The value is relative to the contents rect in the [box model](http://doc.qt.io/qt-5/stylesheet-customizing.html#the-box-model).Example:`QSpinBox { max-height: 24px }`See also [max-width](http://doc.qt.io/qt-5/stylesheet-reference.html#max-width-prop). |
| **max-width**                                   | [Length](http://doc.qt.io/qt-5/stylesheet-reference.html#length) | The widget's or a subcontrol's maximum width.This property is supported by [QAbstractItemView](http://doc.qt.io/qt-5/qabstractitemview.html) subclasses, [QAbstractSpinBox](http://doc.qt.io/qt-5/qabstractspinbox.html)subclasses, [QCheckBox](http://doc.qt.io/qt-5/qcheckbox.html), [QComboBox](http://doc.qt.io/qt-5/qcombobox.html), [QFrame](http://doc.qt.io/qt-5/qframe.html), [QGroupBox](http://doc.qt.io/qt-5/qgroupbox.html), [QLabel](http://doc.qt.io/qt-5/qlabel.html), [QLineEdit](http://doc.qt.io/qt-5/qlineedit.html), [QMenu](http://doc.qt.io/qt-5/qmenu.html), [QMenuBar](http://doc.qt.io/qt-5/qmenubar.html), [QPushButton](http://doc.qt.io/qt-5/qpushbutton.html), [QRadioButton](http://doc.qt.io/qt-5/qradiobutton.html), [QSizeGrip](http://doc.qt.io/qt-5/qsizegrip.html), [QSpinBox](http://doc.qt.io/qt-5/qspinbox.html), [QSplitter](http://doc.qt.io/qt-5/qsplitter.html), [QStatusBar](http://doc.qt.io/qt-5/qstatusbar.html), [QTextEdit](http://doc.qt.io/qt-5/qtextedit.html), and [QToolTip](http://doc.qt.io/qt-5/qtooltip.html).The value is relative to the contents rect in the [box model](http://doc.qt.io/qt-5/stylesheet-customizing.html#the-box-model).Example:`QComboBox { max-width: 72px }`See also [max-height](http://doc.qt.io/qt-5/stylesheet-reference.html#max-height-prop). |
| **messagebox-text-interaction-flags\***         | [Number](http://doc.qt.io/qt-5/stylesheet-reference.html#number) | The interaction behavior for text in a message box. Possible values are based on [Qt::TextInteractionFlags](http://doc.qt.io/qt-5/qt.html#TextInteractionFlag-enum).If this property is not specified, it defaults to the value specified by the current style for the [SH_MessageBox_TextInteractionFlags](http://doc.qt.io/qt-5/qstyle.html#StyleHint-enum) style hint.Example:`QMessageBox { messagebox-text-interaction-flags: 5 }` |
| **min-height**                                  | [Length](http://doc.qt.io/qt-5/stylesheet-reference.html#length) | The widget's or a subcontrol's minimum height.This property is supported by [QAbstractItemView](http://doc.qt.io/qt-5/qabstractitemview.html) subclasses, [QAbstractSpinBox](http://doc.qt.io/qt-5/qabstractspinbox.html)subclasses, [QCheckBox](http://doc.qt.io/qt-5/qcheckbox.html), [QComboBox](http://doc.qt.io/qt-5/qcombobox.html), [QFrame](http://doc.qt.io/qt-5/qframe.html), [QGroupBox](http://doc.qt.io/qt-5/qgroupbox.html), [QLabel](http://doc.qt.io/qt-5/qlabel.html), [QLineEdit](http://doc.qt.io/qt-5/qlineedit.html), [QMenu](http://doc.qt.io/qt-5/qmenu.html), [QMenuBar](http://doc.qt.io/qt-5/qmenubar.html), [QPushButton](http://doc.qt.io/qt-5/qpushbutton.html), [QRadioButton](http://doc.qt.io/qt-5/qradiobutton.html), [QSizeGrip](http://doc.qt.io/qt-5/qsizegrip.html), [QSpinBox](http://doc.qt.io/qt-5/qspinbox.html), [QSplitter](http://doc.qt.io/qt-5/qsplitter.html), [QStatusBar](http://doc.qt.io/qt-5/qstatusbar.html), [QTextEdit](http://doc.qt.io/qt-5/qtextedit.html), and [QToolTip](http://doc.qt.io/qt-5/qtooltip.html).If this property is not specified, the minimum height is derived based on the widget's contents and the style.The value is relative to the contents rect in the [box model](http://doc.qt.io/qt-5/stylesheet-customizing.html#the-box-model).Example:`QComboBox { min-height: 24px }`See also [min-width](http://doc.qt.io/qt-5/stylesheet-reference.html#min-width-prop). |
| **min-width**                                   | [Length](http://doc.qt.io/qt-5/stylesheet-reference.html#length) | The widget's or a subcontrol's minimum width.This property is supported by [QAbstractItemView](http://doc.qt.io/qt-5/qabstractitemview.html) subclasses, [QAbstractSpinBox](http://doc.qt.io/qt-5/qabstractspinbox.html)subclasses, [QCheckBox](http://doc.qt.io/qt-5/qcheckbox.html), [QComboBox](http://doc.qt.io/qt-5/qcombobox.html), [QFrame](http://doc.qt.io/qt-5/qframe.html), [QGroupBox](http://doc.qt.io/qt-5/qgroupbox.html), [QLabel](http://doc.qt.io/qt-5/qlabel.html), [QLineEdit](http://doc.qt.io/qt-5/qlineedit.html), [QMenu](http://doc.qt.io/qt-5/qmenu.html), [QMenuBar](http://doc.qt.io/qt-5/qmenubar.html), [QPushButton](http://doc.qt.io/qt-5/qpushbutton.html), [QRadioButton](http://doc.qt.io/qt-5/qradiobutton.html), [QSizeGrip](http://doc.qt.io/qt-5/qsizegrip.html), [QSpinBox](http://doc.qt.io/qt-5/qspinbox.html), [QSplitter](http://doc.qt.io/qt-5/qsplitter.html), [QStatusBar](http://doc.qt.io/qt-5/qstatusbar.html), [QTextEdit](http://doc.qt.io/qt-5/qtextedit.html), and [QToolTip](http://doc.qt.io/qt-5/qtooltip.html).If this property is not specified, the minimum width is derived based on the widget's contents and the style.The value is relative to the contents rect in the [box model](http://doc.qt.io/qt-5/stylesheet-customizing.html#the-box-model).Example:`QComboBox { min-width: 72px }`See also [min-height](http://doc.qt.io/qt-5/stylesheet-reference.html#min-height-prop). |
| **opacity\***                                   | [Number](http://doc.qt.io/qt-5/stylesheet-reference.html#number) | The opacity for a widget. Possible values are from 0 (transparent) to 255 (opaque). For the moment, this is only supported for [tooltips](http://doc.qt.io/qt-5/qtooltip.html).If this property is not specified, it defaults to the value specified by the current style for the [SH_ToolTipLabel_Opacity](http://doc.qt.io/qt-5/qstyle.html#StyleHint-enum) style hint.Example:`QToolTip { opacity: 223 }` |
| **outline**                                     |                                                              | The outline drawn around the object's border.                |
| **outline-color**                               | [Color](http://doc.qt.io/qt-5/stylesheet-reference.html#color) | The color of the outline. See also [border-color](http://doc.qt.io/qt-5/stylesheet-reference.html#border-color-prop) |
| **outline-offset**                              | [Length](http://doc.qt.io/qt-5/stylesheet-reference.html#length) | The outline's offset from the border of the widget.          |
| **outline-style**                               |                                                              | Specifies the pattern used to draw the outline. See also [border-style](http://doc.qt.io/qt-5/stylesheet-reference.html#border-style-prop) |
| **outline-radius**                              |                                                              | Adds rounded corners to the outline                          |
| **outline-bottom-left-radius**                  | [Radius](http://doc.qt.io/qt-5/stylesheet-reference.html#radius) | The radius for the bottom-left rounded corner of the outline. |
| **outline-bottom-right-radius**                 | [Radius](http://doc.qt.io/qt-5/stylesheet-reference.html#radius) | The radius for the bottom-right rounded corner of the outline. |
| **outline-top-left-radius**                     | [Radius](http://doc.qt.io/qt-5/stylesheet-reference.html#radius) | The radius for the top-left corner of the outline.           |
| **outline-top-right-radius**                    | [Radius](http://doc.qt.io/qt-5/stylesheet-reference.html#radius) | The radius for the top-right rounded corner of the outline.  |
| **padding**                                     | [Box Lengths](http://doc.qt.io/qt-5/stylesheet-reference.html#box-lengths) | The widget's padding. Equivalent to specifying `padding-top`, `padding-right`, `padding-bottom`, and `padding-left`.This property is supported by [QAbstractItemView](http://doc.qt.io/qt-5/qabstractitemview.html) subclasses, [QAbstractSpinBox](http://doc.qt.io/qt-5/qabstractspinbox.html)subclasses, [QCheckBox](http://doc.qt.io/qt-5/qcheckbox.html), [QComboBox](http://doc.qt.io/qt-5/qcombobox.html), [QFrame](http://doc.qt.io/qt-5/qframe.html), [QGroupBox](http://doc.qt.io/qt-5/qgroupbox.html), [QLabel](http://doc.qt.io/qt-5/qlabel.html), [QLineEdit](http://doc.qt.io/qt-5/qlineedit.html), [QMenu](http://doc.qt.io/qt-5/qmenu.html), [QMenuBar](http://doc.qt.io/qt-5/qmenubar.html), [QPushButton](http://doc.qt.io/qt-5/qpushbutton.html), [QRadioButton](http://doc.qt.io/qt-5/qradiobutton.html), [QSplitter](http://doc.qt.io/qt-5/qsplitter.html), [QTextEdit](http://doc.qt.io/qt-5/qtextedit.html), and [QToolTip](http://doc.qt.io/qt-5/qtooltip.html).If this property is not specified, it defaults to `0`.Example:`QLineEdit { padding: 3px }`See also [margin](http://doc.qt.io/qt-5/stylesheet-reference.html#margin-prop), [spacing](http://doc.qt.io/qt-5/stylesheet-reference.html#spacing-prop), and [The Box Model](http://doc.qt.io/qt-5/stylesheet-customizing.html#the-box-model). |
| `padding-top`                                   | [Length](http://doc.qt.io/qt-5/stylesheet-reference.html#length) | The widget's top padding.                                    |
| `padding-right`                                 | [Length](http://doc.qt.io/qt-5/stylesheet-reference.html#length) | The widget's right padding.                                  |
| `padding-bottom`                                | [Length](http://doc.qt.io/qt-5/stylesheet-reference.html#length) | The widget's bottom padding.                                 |
| `padding-left`                                  | [Length](http://doc.qt.io/qt-5/stylesheet-reference.html#length) | The widget's left padding.                                   |
| **paint-alternating-row-colors-for-empty-area** |                            `bool`                            | Whether the [QTreeView](http://doc.qt.io/qt-5/qtreeview.html) paints alternating row colors for the empty area (i.e the area where there are no items) |
| **position**                                    |                   `relative` | `absolute`                    | Whether offsets specified using [left](http://doc.qt.io/qt-5/stylesheet-reference.html#left-prop), [right](http://doc.qt.io/qt-5/stylesheet-reference.html#right-prop), [top](http://doc.qt.io/qt-5/stylesheet-reference.html#top-prop), and [bottom](http://doc.qt.io/qt-5/stylesheet-reference.html#bottom-prop) are relative or absolute coordinates.If this property is not specified, it defaults to `relative`. |
| **right**                                       | [Length](http://doc.qt.io/qt-5/stylesheet-reference.html#length) | If [position](http://doc.qt.io/qt-5/stylesheet-reference.html#position-prop) is `relative` (the default), moves a subcontrol by a certain offset to the left; specifying `right: *x*` is then equivalent to specifying `left: -*x*`.If [position](http://doc.qt.io/qt-5/stylesheet-reference.html#position-prop) is `absolute`, the `right` property specifies the subcontrol's right edge in relation to the parent's right edge (see also [subcontrol-origin](http://doc.qt.io/qt-5/stylesheet-reference.html#subcontrol-origin-prop)).Example:`QSpinBox::down-button { right: 2px }`See also [left](http://doc.qt.io/qt-5/stylesheet-reference.html#left-prop), [top](http://doc.qt.io/qt-5/stylesheet-reference.html#top-prop), and [bottom](http://doc.qt.io/qt-5/stylesheet-reference.html#bottom-prop). |
| **selection-background-color\***                | [Brush](http://doc.qt.io/qt-5/stylesheet-reference.html#brush) | The background of selected text or items.This property is supported by all widgets that respect the [QWidget::palette](http://doc.qt.io/qt-5/qwidget.html#palette-prop) and that show selection text.If this property is not set, the default value is whatever is set for the palette's [Highlight](http://doc.qt.io/qt-5/qpalette.html#ColorRole-enum)role.Example:`QTextEdit { selection-background-color: darkblue }`See also [selection-color](http://doc.qt.io/qt-5/stylesheet-reference.html#selection-color-prop) and [background](http://doc.qt.io/qt-5/stylesheet-reference.html#background-prop). |
| **selection-color\***                           | [Brush](http://doc.qt.io/qt-5/stylesheet-reference.html#brush) | The foreground of selected text or items.This property is supported by all widgets that respect the [QWidget::palette](http://doc.qt.io/qt-5/qwidget.html#palette-prop) and that show selection text.If this property is not set, the default value is whatever is set for the palette's [HighlightedText](http://doc.qt.io/qt-5/qpalette.html#ColorRole-enum) role.Example:`QTextEdit { selection-color: white }`See also [selection-background-color](http://doc.qt.io/qt-5/stylesheet-reference.html#selection-background-color-prop) and [color](http://doc.qt.io/qt-5/stylesheet-reference.html#color-prop). |
| **show-decoration-selected\***                  | [Boolean](http://doc.qt.io/qt-5/stylesheet-reference.html#boolean) | Controls whether selections in a [QListView](http://doc.qt.io/qt-5/qlistview.html) cover the entire row or just the extent of the text.If this property is not specified, it defaults to the value specified by the current style for the [SH_ItemView_ShowDecorationSelected](http://doc.qt.io/qt-5/qstyle.html#StyleHint-enum) style hint.Example:`* { show-decoration-selected: 1 }` |
| **spacing\***                                   | [Length](http://doc.qt.io/qt-5/stylesheet-reference.html#length) | Internal spacing in the widget.This property is supported by [QCheckBox](http://doc.qt.io/qt-5/qcheckbox.html), checkable [QGroupBox](http://doc.qt.io/qt-5/qgroupbox.html)es, [QMenuBar](http://doc.qt.io/qt-5/qmenubar.html), and [QRadioButton](http://doc.qt.io/qt-5/qradiobutton.html).If this property is not specified, the default value depends on the widget and on the current style.Example:`QMenuBar { spacing: 10 }`See also [padding](http://doc.qt.io/qt-5/stylesheet-reference.html#padding-prop) and [margin](http://doc.qt.io/qt-5/stylesheet-reference.html#margin-prop). |
| **subcontrol-origin\***                         | [Origin](http://doc.qt.io/qt-5/stylesheet-reference.html#origin) | The origin rectangle of the subcontrol within the parent element.If this property is not specified, the default is `padding`.Example:`QSpinBox::up-button {    image: url(:/images/spinup.png);    subcontrol-origin: content;    subcontrol-position: right top;}`See also [subcontrol-position](http://doc.qt.io/qt-5/stylesheet-reference.html#subcontrol-position-prop). |
| **subcontrol-position\***                       | [Alignment](http://doc.qt.io/qt-5/stylesheet-reference.html#alignment) | The alignment of the subcontrol within the origin rectangle specified by [subcontrol-origin](http://doc.qt.io/qt-5/stylesheet-reference.html#subcontrol-origin-prop).If this property is not specified, it defaults to a value that depends on the subcontrol.Example:`QSpinBox::down-button {    image: url(:/images/spindown.png);    subcontrol-origin: padding;    subcontrol-position: right bottom;}`See also [subcontrol-origin](http://doc.qt.io/qt-5/stylesheet-reference.html#subcontrol-origin-prop). |
| **titlebar-show-tooltips-on-buttons**}          |                            `bool`                            | Whether tool tips are shown on window title bar buttons.     |
| **widget-animation-duration\***                 | [Number](http://doc.qt.io/qt-5/stylesheet-reference.html#number) | How much an animation should last (in milliseconds). A value equal to zero means that the animations will be disabled.If this property is not specified, it defaults to the value specified by the current style for the [SH_Widget_Animation_Duration](http://doc.qt.io/qt-5/qstyle.html#StyleHint-enum) style hint.**This property was added in Qt 5.10.**Example:`* { widget-animation-duration: 100 }` |
| **text-align**                                  | [Alignment](http://doc.qt.io/qt-5/stylesheet-reference.html#alignment) | The alignment of text and icon within the contents of the widget.If this value is not specified, it defaults to the value that depends on the native style.Example:`QPushButton {    text-align: left;}`This property is currently supported only by [QPushButton](http://doc.qt.io/qt-5/qpushbutton.html) and [QProgressBar](http://doc.qt.io/qt-5/qprogressbar.html). |
| **text-decoration**                             |         `none` `underline``overline` `line-through`          | Additional text effects                                      |
| **top**                                         | [Length](http://doc.qt.io/qt-5/stylesheet-reference.html#length) | If [position](http://doc.qt.io/qt-5/stylesheet-reference.html#position-prop) is `relative` (the default), moves a subcontrol by a certain offset down.If [position](http://doc.qt.io/qt-5/stylesheet-reference.html#position-prop) is `absolute`, the `top` property specifies the subcontrol's top edge in relation to the parent's top edge (see also [subcontrol-origin](http://doc.qt.io/qt-5/stylesheet-reference.html#subcontrol-origin-prop)).If this property is not specified, it defaults to `0`.Example:`QSpinBox::up-button { top: 2px }`See also [left](http://doc.qt.io/qt-5/stylesheet-reference.html#left-prop), [right](http://doc.qt.io/qt-5/stylesheet-reference.html#right-prop), and [bottom](http://doc.qt.io/qt-5/stylesheet-reference.html#bottom-prop). |
| **width**                                       | [Length](http://doc.qt.io/qt-5/stylesheet-reference.html#length) | The width of a subcontrol (or a widget in some cases).If this property is not specified, it defaults to a value that depends on the subcontrol/widget and on the current style.**Warning:** Unless otherwise specified, this property has no effect when set on widgets. If you want a widget with a fixed width, set the [min-width](http://doc.qt.io/qt-5/stylesheet-reference.html#min-width-prop) and [max-width](http://doc.qt.io/qt-5/stylesheet-reference.html#max-width-prop) to the same value.Example:`QSpinBox::up-button { width: 12px }`See also [height](http://doc.qt.io/qt-5/stylesheet-reference.html#height-prop). |

###伪状态码表

| Pseudo-State         | Description                                                  |
| -------------------- | ------------------------------------------------------------ |
| `:active`            | This state is set when the widget resides in an active window. |
| `:adjoins-item`      | This state is set when the [::branch](http://doc.qt.io/qt-5/stylesheet-reference.html#branch-sub) of a [QTreeView](http://doc.qt.io/qt-5/qtreeview.html) is adjacent to an item. |
| `:alternate`         | This state is set for every alternate row whe painting the row of a [QAbstractItemView](http://doc.qt.io/qt-5/qabstractitemview.html) when [QAbstractItemView::alternatingRowColors](http://doc.qt.io/qt-5/qabstractitemview.html#alternatingRowColors-prop)() is set to true. |
| `:bottom`            | The item is positioned at the bottom. For example, a [QTabBar](http://doc.qt.io/qt-5/qtabbar.html) that has its tabs positioned at the bottom. |
| `:checked`           | The item is checked. For example, the [checked](http://doc.qt.io/qt-5/qabstractbutton.html#checked-prop) state of [QAbstractButton](http://doc.qt.io/qt-5/qabstractbutton.html). |
| `:closable`          | The items can be closed. For example, the [QDockWidget](http://doc.qt.io/qt-5/qdockwidget.html) has the [QDockWidget::DockWidgetClosable](http://doc.qt.io/qt-5/qdockwidget.html#DockWidgetFeature-enum) feature turned on. |
| `:closed`            | The item is in the closed state. For example, an non-expanded item in a [QTreeView](http://doc.qt.io/qt-5/qtreeview.html) |
| `:default`           | The item is the default. For example, a [default](http://doc.qt.io/qt-5/qpushbutton.html#default-prop) [QPushButton](http://doc.qt.io/qt-5/qpushbutton.html) or a default action in a [QMenu](http://doc.qt.io/qt-5/qmenu.html). |
| `:disabled`          | The item is [disabled](http://doc.qt.io/qt-5/qwidget.html#enabled-prop). |
| `:editable`          | The [QComboBox](http://doc.qt.io/qt-5/qcombobox.html) is editable. |
| `:edit-focus`        | The item has edit focus (See [QStyle::State_HasEditFocus](http://doc.qt.io/qt-5/qstyle.html#StateFlag-enum)). This state is available only for Qt Extended applications. |
| `:enabled`           | The item is [enabled](http://doc.qt.io/qt-5/qwidget.html#enabled-prop). |
| `:exclusive`         | The item is part of an exclusive item group. For example, a menu item in a exclusive [QActionGroup](http://doc.qt.io/qt-5/qactiongroup.html). |
| `:first`             | The item is the first (in a list). For example, the first tab in a [QTabBar](http://doc.qt.io/qt-5/qtabbar.html). |
| `:flat`              | The item is flat. For example, a [flat](http://doc.qt.io/qt-5/qpushbutton.html#flat-prop) [QPushButton](http://doc.qt.io/qt-5/qpushbutton.html). |
| `:floatable`         | The items can be floated. For example, the [QDockWidget](http://doc.qt.io/qt-5/qdockwidget.html) has the [QDockWidget::DockWidgetFloatable](http://doc.qt.io/qt-5/qdockwidget.html#DockWidgetFeature-enum) feature turned on. |
| `:focus`             | The item has [input focus](http://doc.qt.io/qt-5/qwidget.html#focus-prop). |
| `:has-children`      | The item has children. For example, an item in a [QTreeView](http://doc.qt.io/qt-5/qtreeview.html) that has child items. |
| `:has-siblings`      | The item has siblings. For example, an item in a [QTreeView](http://doc.qt.io/qt-5/qtreeview.html) that siblings. |
| `:horizontal`        | The item has horizontal orientation                          |
| `:hover`             | The mouse is hovering over the item.                         |
| `:indeterminate`     | The item has indeterminate state. For example, a [QCheckBox](http://doc.qt.io/qt-5/qcheckbox.html) or [QRadioButton](http://doc.qt.io/qt-5/qradiobutton.html) is [partially checked](http://doc.qt.io/qt-5/qt.html#CheckState-enum). |
| `:last`              | The item is the last (in a list). For example, the last tab in a [QTabBar](http://doc.qt.io/qt-5/qtabbar.html). |
| `:left`              | The item is positioned at the left. For example, a [QTabBar](http://doc.qt.io/qt-5/qtabbar.html) that has its tabs positioned at the left. |
| `:maximized`         | The item is maximized. For example, a maximized [QMdiSubWindow](http://doc.qt.io/qt-5/qmdisubwindow.html). |
| `:middle`            | The item is in the middle (in a list). For example, a tab that is not in the beginning or the end in a [QTabBar](http://doc.qt.io/qt-5/qtabbar.html). |
| `:minimized`         | The item is minimized. For example, a minimized [QMdiSubWindow](http://doc.qt.io/qt-5/qmdisubwindow.html). |
| `:movable`           | The item can be moved around. For example, the [QDockWidget](http://doc.qt.io/qt-5/qdockwidget.html) has the [QDockWidget::DockWidgetMovable](http://doc.qt.io/qt-5/qdockwidget.html#DockWidgetFeature-enum)feature turned on. |
| `:no-frame`          | The item has no frame. For example, a frameless [QSpinBox](http://doc.qt.io/qt-5/qspinbox.html) or [QLineEdit](http://doc.qt.io/qt-5/qlineedit.html). |
| `:non-exclusive`     | The item is part of a non-exclusive item group. For example, a menu item in a non-exclusive [QActionGroup](http://doc.qt.io/qt-5/qactiongroup.html). |
| `:off`               | For items that can be toggled, this applies to items in the "off" state. |
| `:on`                | For items that can be toggled, this applies to widgets in the "on" state. |
| `:only-one`          | The item is the only one (in a list). For example, a lone tab in a [QTabBar](http://doc.qt.io/qt-5/qtabbar.html). |
| `:open`              | The item is in the open state. For example, an expanded item in a [QTreeView](http://doc.qt.io/qt-5/qtreeview.html), or a [QComboBox](http://doc.qt.io/qt-5/qcombobox.html) or [QPushButton](http://doc.qt.io/qt-5/qpushbutton.html)with an open menu. |
| `:next-selected`     | The next item (in a list) is selected. For example, the selected tab of a [QTabBar](http://doc.qt.io/qt-5/qtabbar.html) is next to this item. |
| `:pressed`           | The item is being pressed using the mouse.                   |
| `:previous-selected` | The previous item (in a list) is selected. For example, a tab in a [QTabBar](http://doc.qt.io/qt-5/qtabbar.html) that is next to the selected tab. |
| `:read-only`         | The item is marked read only or non-editable. For example, a read only [QLineEdit](http://doc.qt.io/qt-5/qlineedit.html) or a non-editable [QComboBox](http://doc.qt.io/qt-5/qcombobox.html). |
| `:right`             | The item is positioned at the right. For example, a [QTabBar](http://doc.qt.io/qt-5/qtabbar.html) that has its tabs positioned at the right. |
| `:selected`          | The item is selected. For example, the selected tab in a [QTabBar](http://doc.qt.io/qt-5/qtabbar.html) or the selected item in a [QMenu](http://doc.qt.io/qt-5/qmenu.html). |
| `:top`               | The item is positioned at the top. For example, a [QTabBar](http://doc.qt.io/qt-5/qtabbar.html) that has its tabs positioned at the top. |
| `:unchecked`         | The item is [unchecked](http://doc.qt.io/qt-5/qabstractbutton.html#checked-prop). |
| `:vertical`          | The item has vertical orientation.                           |
| `:window`            | The widget is a window (i.e top level widget)                |



### Customizing QMainWindow

The separator of a [QMainWindow](http://doc.qt.io/archives/qt-4.8/porting4.html#qmainwindow) can be styled as follows:

```
QMainWindow::separator {
    background: yellow;
    width: 10px; /* when vertical */
    height: 10px; /* when horizontal */
}

QMainWindow::separator:hover {
    background: red;
}
```

For a more advanced customization, use a style sheet as follows:

```
QMenu {
    background-color: white;
    margin: 2px; /* some spacing around the menu */
}

QMenu::item {
    padding: 2px 25px 2px 20px;
    border: 1px solid transparent; /* reserve space for selection border */
}

QMenu::item:selected {
    border-color: darkblue;
    background: rgba(100, 100, 100, 150);
}

QMenu::icon:checked { /* appearance of a 'checked' icon */
    background: gray;
    border: 1px inset gray;
    position: absolute;
    top: 1px;
    right: 1px;
    bottom: 1px;
    left: 1px;
}

QMenu::separator {
    height: 2px;
    background: lightblue;
    margin-left: 10px;
    margin-right: 5px;
}

QMenu::indicator {
    width: 13px;
    height: 13px;
}

/* non-exclusive indicator = check box style indicator (see QActionGroup::setExclusive) */
QMenu::indicator:non-exclusive:unchecked {
    image: url(:/images/checkbox_unchecked.png);
}

QMenu::indicator:non-exclusive:unchecked:selected {
    image: url(:/images/checkbox_unchecked_hover.png);
}

QMenu::indicator:non-exclusive:checked {
    image: url(:/images/checkbox_checked.png);
}

QMenu::indicator:non-exclusive:checked:selected {
    image: url(:/images/checkbox_checked_hover.png);
}

/* exclusive indicator = radio button style indicator (see QActionGroup::setExclusive) */
QMenu::indicator:exclusive:unchecked {
    image: url(:/images/radiobutton_unchecked.png);
}

QMenu::indicator:exclusive:unchecked:selected {
    image: url(:/images/radiobutton_unchecked_hover.png);
}

QMenu::indicator:exclusive:checked {
    image: url(:/images/radiobutton_checked.png);
}

QMenu::indicator:exclusive:checked:selected {
    image: url(:/images/radiobutton_checked_hover.png);
}
```

## 界面切换方式



```界面切换
void MainWindow::on_pushButton_clicked()  
{  
    this->hide();//主界面关闭  
    video *videodlg = new video(this);//新建子界面  
    connect(videodlg,SIGNAL(sendsignal()),this,SLOT(reshow()));//当点击子界面时，调用主界面的reshow()函数  
    videodlg->show();//子界面出现  
}  

//子界面

#ifndef VIDEO_H  
#define VIDEO_H  
  
#include <QDialog>  
  
namespace Ui {  
class video;  
}  
  
class video : public QDialog  
{  
    Q_OBJECT  
  
public:  
    explicit video(QWidget *parent = 0);  
    ~video();  
private slots:  
    void on_pushButton_clicked();  
signals:  
    void sendsignal();//这个函数用户向主界面通知关闭的消息  
  
private:  
    Ui::video *ui;  
};  
  
#endif // VIDEO_H  



//销毁子界面内容 1的话会删除所有子界面对象 应用方式类似于属性值设定
this->setAttribute(Qt::WA_DeleteOnClose,1);  

```



### 界面切换方案1

使用hide隐藏主界面，设置与子界面的信号槽关系，然后show出子界面，然后关闭子界面emit出信号激活主界面的show函数

### 界面切换方案2*

使用Qt中的stackedLayout布局方式，通过切换Qt::setCurrentIndex()的方法使用信号中的数字切换对应窗口

## 分辨率适配

有一个获取屏幕分辨率的函数 

```
QDesktopWidget *desktop =QApplication::desktop();
QRect deskRect = desktop->availableGeometry(); //获取可用桌面大小
QRect screenRect = desktop->screenGeometry(); //获取屏幕大小
```



## 列表自动调整列宽

```
ui->tableWidget->horizontalHeader()->setSectionResizeMode(QHeaderView::Stretch);
```



## *Windows部署工具

http://doc.qt.io/qt-5/windows-deployment.html

Windows部署工具旨在自动执行创建可部署文件夹的过程，该文件夹包含从该文件夹运行应用程序所需的与[Qt](http://doc.qt.io/qt-5/qmake-variable-reference.html#qt)相关的依赖关系（库，QML导入，插件和翻译）。它为[Windows运行时](http://doc.qt.io/qt-5/winrt-support.html)创建沙箱或为Windows桌面应用程序创建安装树，可以轻松将其捆绑到安装包中。

该工具可以在中找到`QTDIR/bin/windeployqt`。它将`.exe`包含`.exe`文件的文件或目录作为参数，并扫描可执行文件的依赖关系。如果使用`--qmldir`参数传递目录，则`windeployqt`使用该`qmlimportscanner`工具扫描目录中的QML文件以获取QML导入依赖关系。然后将识别的依赖关系复制到可执行文件的目录中。Qt5Core.dll中的硬编码本地路径进一步被相关的替换。

对于Windows桌面应用程序，默认情况下编译器所需的运行时文件也会复制到可部署文件夹（除非`--no-compiler-runtime`指定了该选项）。在使用Microsoft Visual C ++进行发布构建的情况下，这些包含Visual C ++可再发行组件包，这些包可用于目标计算机上应用程序安装程序的递归安装。否则，使用编译器运行时的共享库。

该应用程序可能需要额外的第三方库（例如数据库库），windeployqt不会考虑这些库。

其他参数在工具的帮助输出中进行了描述：

```
用法：windeployqt [options] [files]
Qt部署工具5.9.0

使用windeployqt最简单的方法是添加Qt的bin目录
安装（例如<QT_DIR \ bin>）到PATH变量，然后运行：
  windeployqt <path-to-app-binary>
如果ICU，ANGLE等不在bin目录中，则需要在PATH中
变量。如果您的应用程序使用Qt Quick，请运行：
  windeployqt --qmldir <path-to-app-qml-files> <path-to-app-binary>
```

| `qwindows` | QWindowsIntegrationPlugin | [Windows支持](http://doc.qt.io/qt-5/windows-support.html) |
| ---------- | ------------------------- | --------------------------------------------------------- |
|            |                           |                                                           |

### 静态链接

要构建静态应用程序，通过配置Qt来静态构建Qt `-static`：

```
cd C ：\ path \ to \ Qt 
configure - static <你需要的任何其他选项> 
```

如果以后需要从同一位置重新配置和重新构建Qt，请确保先前配置的所有跟踪都通过输入构建目录并运行`nmake distclean`或`mingw32-make distclean`在`configure`再次运行之前删除。

#### 将应用程序链接到Qt的静态版本

作为一个例子，本节将静态构建[Plug＆Paint](http://doc.qt.io/qt-5/qtwidgets-tools-plugandpaint-app-example.html)示例。

一旦Qt完成构建，构建[Plug＆Paint](http://doc.qt.io/qt-5/qtwidgets-tools-plugandpaint-app-example.html)应用程序。首先，我们必须进入包含应用程序的目录：

```
cd examples \ tools \ plugandpaint
```

运行`qmake`以为应用程序创建一个新的生成文件，并执行一个干净的生成以创建静态链接的可执行文件：

```
nmake clean
qmake -config release
nmake
```

您可能希望链接到发行版库，并且可以在调用时指定`qmake`。现在，假设所有编译和链接没有任何错误，我们应该有一个`plugandpaint.exe`准备好部署的文件。要检查应用程序是否具有所需的库，请将可执行文件复制到没有安装Qt或任何Qt应用程序的计算机上，然后在该计算机上运行它。

请记住，如果您的应用程序依赖于编译器特定的库，则这些库仍然必须与应用程序一起重新分发。您可以使用该`depends`工具检查应用程序链接的库。有关更多信息，请阅读[Application Dependencies](http://doc.qt.io/qt-5/windows-deployment.html#application-dependencies)部分。

由于我们无法使用静态链接方式部署插件，因此我们准备的应用程序不完整。它会运行，但由于缺少插件，功能将被禁用。要部署基于插件的应用程序，我们应该使用共享库方法。

### 共享库 Shared Libraries

在使用共享库方法部署[Plug＆Paint](http://doc.qt.io/qt-5/qtwidgets-tools-plugandpaint-app-example.html)应用程序时，我们遇到两个挑战：Qt运行时必须与应用程序可执行文件一起正确地重新分发，并且插件必须安装在目标系统上的正确位置，以便应用程序可以找到他们。

#### 将Qt构建为共享库

在这个例子中，我们假设Qt在 C:\path\to\Qt directory目录中安装为共享库，这是安装Qt时的默认库。

#### 将应用程序链接到Qt作为共享库Linking the Application to Qt as a Shared Library

确保Qt构建为共享库之后，我们可以构建[Plug＆Paint](http://doc.qt.io/qt-5/qtwidgets-tools-plugandpaint-app-example.html)应用程序。首先，我们必须进入包含应用程序的目录：

```
cd examples \ tools \ plugandpaint
```

现在运行`qmake`为应用程序创建一个新的生成文件，并执行一个干净的生成来创建动态链接的可执行文件：

```
干净
qmake - config发布
NMAKE
```

这构建了核心应用程序，下面将构建插件：

```
cd 。。\ plugandpaint /插件
干净
qmake - config发布
NMAKE
```

如果一切都编译和链接没有任何错误，我们将得到一个`plugandpaint.exe`可执行文件`pnp_basictools.dll`和`pnp_extrafilters.dll`插件文件。



#### 创建应用程序包Creating the Application Package

要部署应用程序，我们必须确保将相关的Qt DLL（对应于应用程序中使用的Qt模块）和Windows平台插件`qwindows.dll`以及可执行文件复制到`release`子目录中的相同目录树。

与用户插件相比，Qt插件必须放入与插件类型匹配的子目录中。平台插件的正确位置是一个名为的子目录`platforms`。[Qt插件](http://doc.qt.io/qt-5/windows-deployment.html#qt-plugins)部分提供了关于插件的附加信息以及Qt如何搜索插件。

如果[ANGLE](https://chromium.googlesource.com/angle/angle/+/master/README.md)使用或动态的OpenGL（默认），您需要额外包括`libEGL.dll`并`libGLESv2.dll`从Qt的`lib`目录，以及从的DirectX HLSL编译器。HLSL编译器库d3dcompiler_XX.dll，其中XX是ANGLE（libGLESv2）链接的版本号。

如果Qt配置为与ICU或OpenSSL链接，则也需要将相应的DLL添加到该`release`文件夹中。

**注意：**[Qt WebEngine](http://doc.qt.io/qt-5/qtwebengine-index.html)应用程序具有在[部署Qt WebEngine应用程序](http://doc.qt.io/qt-5/qtwebengine-deploying.html)中列出的其他要求。

请记住，如果您的应用程序依赖于编译器特定的库，则这些库必须与应用程序一起重新分发。您可以使用该`depends`工具检查应用程序链接的库。有关更多信息，请参阅[应用程序依赖项](http://doc.qt.io/qt-5/windows-deployment.html#application-dependencies)部分。

我们很快会介绍这些插件，但首先我们将检查应用程序是否可以在部署环境中工作：将可执行文件和Qt DLL复制到没有安装Qt或任何Qt应用程序的计算机，或者如果您想要在构建机器上测试，确保机器在其环境中没有Qt。

如果应用程序启动没有任何问题，那么我们已经成功地创建了[Plug＆Paint](http://doc.qt.io/qt-5/qtwidgets-tools-plugandpaint-app-example.html)应用程序的动态链接版本。但是由于我们还没有部署相关的插件，所以应用程序的功能仍然会丢失。

插件对普通DLL的工作方式不同，所以我们不能像我们用Qt DLL那样将它们复制到与应用程序可执行文件相同的目录中。在寻找插件时，应用程序在`plugins`应用程序可执行文件的目录内的子目录中搜索。

为了让插件可用于我们的应用程序，我们必须创建`plugins`子目录并复制相关的DLL：

```
plugins\pnp_basictools.DLL
plugins\pnp_extrafilters.DLL
```

分发运行[Plug＆Paint](http://doc.qt.io/qt-5/qtwidgets-tools-plugandpaint-app-example.html)应用程序所需的所有Qt DLL和特定应用程序插件的归档文件必须包含以下文件：

| 零件               | 文件名                         |
| ------------------ | ------------------------------ |
| 可执行文件         | `plugandpaint.exe`             |
| 基本工具插件       | `plugins\pnp_basictools.dll`   |
| ExtraFilters插件   | `plugins\pnp_extrafilters.dll` |
| Qt Windows平台插件 | `platforms\qwindows.dll`       |
| Qt核心模块         | `Qt5Core.dll`                  |
| Qt GUI模块         | `Qt5Gui.dll`                   |
| Qt Widgets模块     | `Qt5Widgets.dll`               |

此外，根据您的Visual Studio版本，存档必须包含以下编译器特定的库：

|            | VC ++ 12.0（2013） | VC ++ 14.0（2015）                    |
| ---------- | ------------------ | ------------------------------------- |
| C运行时    | `msvcr120.dll`     | `vccorlib140.dll`, `vcruntime140.dll` |
| C ++运行时 | `msvcp120.dll`     | `msvcp140.dll`                        |

如果Qt配置为使用ICU，则存档文件必须包含：

| 文件名      |             |             |
| ----------- | ----------- | ----------- |
| icudtXX.dll | icuinXX.dll | icuucXX.dll |

最后，如果使用ANGLE，则档案必须另外包含：

| 文件名     |               |                    |
| ---------- | ------------- | ------------------ |
| libEGL.dll | libGLESv2.dll | d3dcompiler_XX.dll |

要验证应用程序现在可以成功部署，可以在没有Qt的计算机上提取此归档文件，并且不安装任何编译器，然后尝试运行它。

将plugins插入plugins子目录的另一种方法是在使用[QCoreApplication :: addLibraryPath](http://doc.qt.io/qt-5/qcoreapplication.html#addLibraryPath)（）或[QCoreApplication :: setLibraryPaths](http://doc.qt.io/qt-5/qcoreapplication.html#setLibraryPaths)（）启动应用程序时添加自定义搜索路径。

```
QCoreApplication :: addLibraryPath（ “C：/ some / other / path”）;
```

使用插件的一个好处是可以轻松地将它们提供给整个应用程序系列。

在创建[QApplication](http://doc.qt.io/qt-5/qapplication.html)对象`main()`之后，在应用程序函数中添加路径通常是最方便的。一旦路径被添加，应用程序将搜索它的插件，除了查看应用程序自己的目录中的子目录。可以添加任何数量的附加路径。`plugins`

### 应用程序依赖关系

#### 清单文件 Manifest files

部署使用Visual Studio编译的应用程序时，还需要执行一些其他步骤。

首先，我们需要复制链接应用程序时创建的清单文件。此清单文件包含有关应用程序在并行程序集（如运行时库）上的依赖关系的信息。

清单文件需要被复制到**与**应用程序可执行文件**相同的**文件夹中。您不需要复制共享库（DLL）的清单文件，因为它们不被使用。

如果共享库的依赖性与使用它的应用程序不同，则需要将清单文件嵌入到DLL二进制文件中。由于Qt 4.1.3，以下`CONFIG`选项可用于嵌入清单：

```
embed_manifest_dll
embed_manifest_exe
```

这两个选项都默认启用。要删除`embed_manifest_exe`，添加

```
CONFIG -  = embed_manifest_exe
```

到您的.pro文件。

您可以在[MSDN网站上](http://msdn.microsoft.com/en-us/library/aa376307.aspx)找到有关清单文件和并行程序集的更多信息。

将运行时库包含在应用程序中的正确方法是确保它们安装在最终用户的系统上。

要将运行时库安装在最终用户的系统上，需要在应用程序中包含适当的Visual C ++可再发行组件包（VCRedist）可执行文件，并确保在用户安装应用程序时执行它。

它们被命名为`vcredist_x64.exe`（IA64和64位）或`vcredist_x86.exe`（32位），可以在文件夹c {Visual Studio安装路径> / VC / redist / <语言代码>}中找到。

或者，也可以从Web下载它们，例如[Visual Studio 2015的vcredist_x64.exe](https://www.microsoft.com/download/details.aspx?id=48145)。

**注意：**您发布的应用程序必须使用与完全相同的C运行时版本相同的编译器版本进行编译。这可以防止由不同版本的C运行时库导致的部署错误。









#### 附加库 Additional Libraries

根据配置的不同，编译器特定的库必须与应用程序一起重新分发。

例如，如果Qt是使用[ANGLE](https://chromium.googlesource.com/angle/angle/+/master/README.md)构建的，那么它的共享库和DirectX的HLSL编译器也将被发布。

您可以使用[Dependency Walker](http://www.dependencywalker.com/)工具来检查应用程序链接的库。所有你需要做的就是像这样运行它：

```
depends <application executable>
```

这将提供应用程序所依赖的库的列表以及其他信息。

![img](C:\Users\ZhangConghai\Pictures\Typora\deployment-windows-depends.png)

`plugandpaint.exe`使用该`depends`工具查看Plug＆Paint可执行文件（）的发布版本时，该工具列出了以下对非系统库的直接依赖关系：

| Qt的                                                         | VC ++ 12.0（2013）                              | VC ++ 14.0（2015）                                           | [MinGW的](https://mingw-w64.org/)                         |
| ------------------------------------------------------------ | ----------------------------------------------- | ------------------------------------------------------------ | --------------------------------------------------------- |
| QT5CORE.DLL - [QtCore](http://doc.qt.io/qt-5/qtcore-module.html)运行时QT5GUI.DLL - [QtGui](http://doc.qt.io/qt-5/qtgui-module.html)运行时QT5WIDGETS.DLL - [QtWidgets](http://doc.qt.io/qt-5/qtwidgets-module.html)运行时 | MSVCR120.DLL - C运行时MSVCP120.DLL - C ++运行库 | VCCORLIB140.DLL，VCRUNTIME140D.DLL - C运行时MSVCP140.DLL - C ++运行库 | LIBWINPTHREAD-1.DLLLIBGCC_S_DW2-1.DLL的libstdc ++ - 6.DLL |

在查看插件DLL时，会列出完全相同的依赖关系。

从Qt 5.2版开始，官方支持的OpenSSL版本是1.0.0或更高版本。版本> = 0.9.7和<1.0.0可能有效，但不能保证。

### Qt插件

所有Qt GUI应用程序都需要一个在Qt 5 中实现[Qt平台抽象](http://doc.qt.io/qt-5/qpa.html)（QPA）层的插件`qwindows.dll`。对于Windows，平台插件的名称是。该文件必须位于`platforms`分发目录下的特定子目录（默认情况下）。或者，如下所述，可以调整搜索路径Qt用来查找其插件。

您的应用程序也可能依赖于一个或多个Qt插件，如打印支持插件，JPEG图像格式插件或SQL驱动程序插件。一定要分发你的应用程序所需的任何Qt插件。类似于平台的插件，插件的每种类型必须位于一个特定的子目录（如内`printsupport`，`imageformats`或`sqldrivers`）分发目录中。

Qt插件的搜索路径被硬编码到[QtCore](http://doc.qt.io/qt-5/qtcore-module.html)库中。默认情况下，Qt安装的plugins子目录是第一个插件搜索路径。但是，预设路径（如默认路径）有一定的缺点。例如，它们可能不存在于目标机器上。出于这个原因，你需要检查各种替代方案以确保找到Qt插件：

- [使用`qt.conf`](http://doc.qt.io/qt-5/qt-conf.html)。如果您在不同地方共享相同插件的可执行文件，建议使用此方法。
- 使用[QApplication :: addLibraryPath](http://doc.qt.io/qt-5/qcoreapplication.html#addLibraryPath)（）或[QApplication :: setLibraryPaths](http://doc.qt.io/qt-5/qcoreapplication.html#setLibraryPaths)（）。如果您只有一个将使用该插件的可执行文件，则建议使用此方法。
- 使用第三方安装实用程序更改[QtCore](http://doc.qt.io/qt-5/qtcore-module.html)库中的硬编码路径。

如果使用[QApplication :: addLibraryPath](http://doc.qt.io/qt-5/qcoreapplication.html#addLibraryPath)添加自定义路径，它可能如下所示：

```
QCoreApplication :: addLibraryPath（ “C：/ customPath / plugins”）;
```

然后[QCoreApplication :: libraryPaths](http://doc.qt.io/qt-5/qcoreapplication.html#libraryPaths)（）会返回如下所示的内容：

- `C:/customPath/plugins`
- `C:/Qt/%VERSION%/plugins`
- `E:/myApplication/directory`

可执行文件将在这些目录中查找插件，并且与[QCoreApplication :: libraryPaths](http://doc.qt.io/qt-5/qcoreapplication.html#libraryPaths)（）返回的[QStringList的](http://doc.qt.io/qt-5/qstringlist.html)顺序相同。新添加的路径预先添加到[QCoreApplication :: libraryPaths](http://doc.qt.io/qt-5/qcoreapplication.html#libraryPaths)（），这意味着它将首先被搜索。但是，如果您使用[QCoreApplication :: setLibraryPaths](http://doc.qt.io/qt-5/qcoreapplication.html#setLibraryPaths)（），您将能够确定将搜索哪些路径和顺序。

该[如何创建Qt的插件](http://doc.qt.io/qt-5/plugins-howto.html)文件概述了您需要注意构建和Qt应用程序部署插件时的问题。

## 进程管理

```c++
//工程名字为myProcess
void MainWindow::on_pushButton_clicked()
{
     myProcess.start("notepad.exe");
}
```

设置外部调用cmd

```c++
QString program = "cmd.exe";
    QStringList arguments;
    arguments << "/c dir&pause";
    myProcess.start(program, arguments);
```

```C++
void MainWindow::showResult()
{
    qDebug() << "showResult: " << endl
            << QString(myProcess.readAll());
}
//读取进程返回值
```

设置外部进程的`readyRead`作为信号，绑定内部`showResult`函数作为槽函数

```c++
//保证中文编码可读
QTextCodec::setCodecForCStrings(QTextCodec::codecForLocale());
```

## 定时

`msleep(1000)`定时1s





## 线程管理

Qt提供了对线程的支持，这包括一组与平台无关的线程类，一个线程安全的发送事件的方式，以及跨线程的信号-槽的关联。这些使得可以很容易的开发可移植的多线程Qt应用程序，可以充分利用多处理器的机器。多线程编程也可以有效的解决在不冻结一个应用程序的用户界面的情况下执行一个耗时的操作的问题。关于线程的内容，大家可以在Qt帮助中参考Thread Support in Qt关键字。

**（一）启动一个线程**

Qt中的`QThread`类提供了平台无关的线程。一个`QThread`代表了一个在应用程序中可以独立控制的线程，它与进程中的其他线程分享数据，但是是独立执行的。相对于一般的程序都是从`main()`函数开始执行，`QThread`从`run()`函数开始执行。默认的，`run()`通过调用`exec()`来开启事件循环。要创建一个线程，需要子类化`QThread`并且重新实现`run()`函数。

每一个线程可以有自己的事件循环，可以通过调用`exec()`函数来启动事件循环，可以通过调用`exit()`或者`quit()`来停止事件循环。在一个线程中拥有一个事件循环，可以使它能够关联其他线程中的信号到本线程的槽上，这使用了队列关联机制，就是在使用`connect()`函数进行信号和槽的关联时，将`Qt::ConnectionType`类型的参数指定为`Qt::QueuedConnection`。拥有事件循环还可以使该线程能过使用需要事件循环的类，比如`QTimer`和`QTcpSocket`类等。注意，在线程中是无法使用任何的部件类的。

向项目中添加新的C++类，类名设置为`MyThread`，基类设置为`QThread`，类型信息选择“继承自QObject”。完成后进入`mythread.h`文件，先添加一个公有函数声明：

```c++
void stop();
```

然后再添加一个函数声明和一个变量的定义：

```c++
protected:
    void run();
private:
    volatile bool stopped;
```

这里`stopped`变量使用了`volatile`关键字，这样可以使它在任何时候都保持最新的值，从而可以避免在多个线程中访问它时出错。然后进入`mythread.cpp`文件中，先添加头文件`#include <QDebug>`，然后在构造函数中添加如下代码：

```
stopped = false;
```

这里将`stopped`变量初始化为`false`。下面添加`run()`函数的定义：

```
void MyThread::run()
{
    qreal i = 0;
    while (!stopped)
        qDebug() << QString("in MyThread: %1").arg(i++);
    stopped = false;
}
```

这里一直判断`stopped`变量的值，只要它为`false`，那么就一直打印字符串。下面添加`stop()`函数的定义：













##捕获鼠标点击坐标













## 撤销类UndoStack

包含两个，1是栈UndoStack，2是压入的UndoCommand类，通常需要重写UndoCommand类（继承QUndoCommand类），主要Imply的Command类中的函数有，undo，redo，id，mergeWith   ，push之后redo会被默认执行， redo以及merge之后会调用isObsolete判断，如果判断结果是True，当前的cmd或者merge的cmd将会被删除。
无论什么情况cmd压入之后永远在栈顶，并且一但将cmd压入UndoStack，UndoStack拥有所有权，并且没有任何的getter能够访问这个cmd命令，因为一旦对cmd作出修改会导致整个栈结构的损坏。





## UDP/TCP通信

使用网络之前必须在.pro文件中声明==QT += network==，不然报错

### UDP

问题 **丢包 失真 无序**

* 丢包:降低通讯频率,重复?
* 失真:校验
* 无序:加入包序号

**发送**

指定ip以及数据报内容，然后发送

```c++
#include <QtNetwork>
QUdpSocket *sender;
sender = new QUdpSocket(this);
QByteArray datagram = “hello world!”;
//UDP广播
sender->writeDatagram(datagram.data(),datagram.size(),QHostAddress::Broadcast,6665);
//向特定IP发送
QHostAddress serverAddress = QHostAddress("10.21.11.66");
sender->writeDatagram(datagram.data(), datagram.size(),serverAddress, 6665);
/* writeDatagram函数原型，发送成功返回字节数，否则-1
qint64 writeDatagram(const char *data,qint64 size,const QHostAddress &address,quint16 port)
qint64 writeDatagram(const QByteArray &datagram,const QHostAddress &host,quint16 port)
*/
```



**接收**

```c++
#include <QtNetwork>
QUdpSocket *receiver;
//信号槽
private slots:  
    void readPendingDatagrams(); 
receiver = new QUdpSocket(this);
receiver->bind(QHostAddress::LocalHost, 6665);
connect(receiver, SIGNAL(readyRead()),this, SLOT(readPendingDatagrams()));
void readPendingDatagrams()
 {
     while (receiver->hasPendingDatagrams()) {
         QByteArray datagram;
         datagram.resize(receiver->pendingDatagramSize());
         receiver->readDatagram(datagram.data(), datagram.size());
         //数据接收在datagram里
/* readDatagram 函数原型
qint64 readDatagram(char *data,qint64 maxSize,QHostAddress *address=0,quint16 *port=0)
*/
     }
 }
```

### TCP

Tcp为cs结构,客户端发送连接请求,服务端响应

**客户端**

```c++
#include <QtNetwork>
QTcpSocket *client;
char *data="hello qt!";
client = new QTcpSocket(this);
client->connectToHost(QHostAddress("10.21.11.66"), 6665);
client->write(data);
```

**服务端**

```c++
#include <QtNetwork>
QTcpServer *server;
QTcpSocket *clientConnection;
server = new QTcpServer();
server->listen(QHostAddress::Any, 6665);
connect(server, SIGNAL(newConnection()), this, SLOT(acceptConnection()));
void acceptConnection()
{
    clientConnection = server->nextPendingConnection();
    connect(clientConnection, SIGNAL(readyRead()), this, SLOT(readClient()));
}
void readClient()
{
    QString str = clientConnection->readAll();
    //或者
    char buf[1024];
    clientConnection->read(buf,1024);
}
```











## 动画

```c++
#include <QPropertyAnimation>
QPropertyAnimation *animation = new QPropertyAnimation(ui->pushButton, "geometry");
animation->setDuration(10000);
animation->setStartValue(ui->pushButton->geometry());
animation->setEndValue(QRect(200, 200, 100, 50));
animation->start();
//设置移动动画
//设置渐进展开
//QRect(left,top,width,height);
//    animation->setStartValue(QRect(0,21,183,0));
//    animation->setEndValue(QRect(0,21,183,602));
```













# 质检仪&电表项目方案

## 工控机前后端交互

必须采用**非阻塞方式**不能影响GUI的图形显示以及后台MQTT数据到云的通道

### 分离方案

移植当前win32的采集发送程序到Qt平台，并且通过socket接口与Qt前端进行交互，==进程间数据交互接口==

```GUI下响应
#Qt前端使用信号槽与后台进行连接，以后台信号作为信号SIGNAL，打开SLOT进行数据研判分析处理
可能也需要设置后台线程，避免响应阻塞GUI
```



### 整合方案

整合win32程序作为程序内的独立线程，进行数据采集，MQTT协议发送以及数据共享

有一个想法，一个客户端，有GUI界面的同时也要向网络服务器发送本地采集的数据，通过网络发送数据的接口是同步阻塞的，需要等待服务器响应数据。

如果不采用后台线程的方案，用主UI线程关联一个定时器QTimer来做定时任务发送，那么GUI界面会由于定时器事件响应的槽函数的阻塞，导致整个GUI的按钮事件在某些时刻无法及时响应，会有卡顿的情况。

所以，后台需要运行一个后台线程，与主线程分离，并且该后台线程需要关联一个自己的QTimer，来做定时任务数据采集。

方案其实可以用QThread来做，首先，做一个Task Class:

```线程
#include <QTimer>
#include <QObject>

class MyTaskClass : public QObject
{
    Q_OBJECT
public:
    explicit MyClass(QObject *parent = 0)
   {
      m_task_timer.setInterval(5000);
    
       connect(&m_task_timer, SIGNAL(timeout()), this,    SLOT(dataCollectionSendTask()));

    m_task_timer.start();
   }
public slots:
    void dataCollectionSendTask()
   {
     //调用网络接口发送采集完成的数据，发送的服务器
    }
private:
    QTimer m_task_timer;
};
```

将设置好的任务移入`backgroundThread`

```task移入后台线程
#include <QtWidgets/QApplication>
#include <QThread>
#include "MyTaskClass.hpp"

int main(int argc, char *argv[])
{
    QApplication a(argc, argv);
    
    QThread* backgroundThread = new QThread;
    backgroundThread->start();
     
     MyTaskClass *task = new MyTaskClass();
     task->moveToThread(backgroundThread);
     
    MainWindow w;
    w.show();
    return a.exec();
}
```

# QT的进程间交互

Qt provides several ways to implement Inter-Process Communication (IPC) in Qt applications.

## TCP/IP    Socket接口

The cross-platform [Qt Network](http://doc.qt.io/qt-5/qtnetwork-index.html) module provides classes that make network programming portable and easy. It offers high-level classes (e.g., [QNetworkAccessManager](http://doc.qt.io/qt-5/qnetworkaccessmanager.html), QFtp) that communicate using specific application-level protocols, and lower-level classes (e.g., [QTcpSocket](http://doc.qt.io/qt-5/qtcpsocket.html), [QTcpServer](http://doc.qt.io/qt-5/qtcpserver.html), [QSslSocket](http://doc.qt.io/qt-5/qsslsocket.html)) for implementing protocols.

## Shared Memory    共享内存

The cross-platform shared memory class, [QSharedMemory](http://doc.qt.io/qt-5/qsharedmemory.html), provides access to the operating system's shared memory implementation. It allows safe access to shared memory segments by multiple threads and processes. Additionally, [QSystemSemaphore](http://doc.qt.io/qt-5/qsystemsemaphore.html) can be used to control access to resources shared by the system, as well as to communicate between processes.

## D-Bus protocol <u>Unix-Only</u>

The [Qt D-Bus](http://doc.qt.io/qt-5/qtdbus-index.html) module is a Unix-only library you can use to implement IPC using the D-Bus protocol. It extends Qt's [Signals and Slots](http://doc.qt.io/qt-5/signalsandslots.html)mechanism to the IPC level, allowing a signal emitted by one process to be connected to a slot in another process. The [Qt D-Bus](http://doc.qt.io/qt-5/qtdbus-index.html)documentation has detailed information on how to use the [Qt D-Bus](http://doc.qt.io/qt-5/qtdbus-index.html) module.

## QProcess Class   子进程管理器

The cross-platform class [QProcess](http://doc.qt.io/qt-5/qprocess.html) can be used to start external programs as child processes, and to communicate with them. It provides an API for monitoring and controlling the state of the child process. [QProcess](http://doc.qt.io/qt-5/qprocess.html) gives access to the input/output channels of child process by inheriting from [QIODevice](http://doc.qt.io/qt-5/qiodevice.html).

## Session Management <u>Unix-Only</u>

In Linux/X11 platforms, Qt provides support for session management. Sessions allow events to be propagated to processes, for example, to notify when a shutdown occurs. The process and applications can then perform any necessary operations such as save open documents.

- [Session Management](http://doc.qt.io/qt-5/session.html)



# 机器人寻路交互界面

![873DE99FD84DEF223AC951EEB4284807](/Users/apple/Desktop/markdown/pic/873DE99FD84DEF223AC951EEB4284807.jpg)

![1523502302792](C:\Users\ZhangConghai\Pictures\Typora\1523502302792.png)

==目标分辨率：==

**功能分解:**

| 功能                      | 实现方式              |
| ------------------------- | --------------------- |
| 下位机通讯                | UDP Socket+Thread     |
| **交互按钮**              | 派生按钮类+stylesheet |
|                           |                       |
|                           |                       |
|                           |                       |
|                           |                       |
| 地图显示界面              |                       |
|                           |                       |
| 地图编辑+修改保存==方式== |                       |
|                           |                       |
|                           |                       |
| 车体位置显示              |                       |
|                           |                       |
| 路径显示                  |                       |
|                           |                       |
| ==**标记车位id**==        |                       |
|                           |                       |
| 当前状态                  |                       |
|                           |                       |

#### 通信协议内容:

1. 位置
2. 状态
   *  ​
   *  ​
   * ​
3. 地图？
4. 目标



