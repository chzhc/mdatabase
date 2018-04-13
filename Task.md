---
typora-copy-images-to: ..\Pictures\Typora
---

# Task

## 4.8日

* [x] 外壳设计
* [ ] 决定与后台的交互方式——数据的来源
* [ ] ​

![1523165822048](C:\Users\ZhangConghai\Pictures\Typora\1523165822048.png)



![1523165845912](C:\Users\ZhangConghai\Pictures\Typora\1523165845912.png)



![1523165877899](C:\Users\ZhangConghai\Pictures\Typora\1523165877899.png)

![1523166166926](C:\Users\ZhangConghai\Pictures\Typora\1523166166926.png)



完成mask，提示当前页面片![1523165889477](C:\Users\ZhangConghai\Pictures\Typora\1523165889477.png)

![1523171135323](C:\Users\ZhangConghai\Pictures\Typora\1523171135323.png)

完成mask 设置按钮disable











## 4.4日



* [ ] 重新搭建主界面，应用stackedLayout
* [ ] 使用ui重新开始设计
* [ ] ​

后续考虑使用QSS样式表，通过加载资源文件的方式进行加载[QSS](https://blog.csdn.net/u010258235/article/details/47778785)

```
this->setWindowFlags(Qt::FramelessWindowHint);//设置无边框
```





## 4.3日

* [ ] 开始**搭建主界面**并进行初步美化
* [ ] 将png图贴上去
* [ ] 设置pushbutton的边框大小
* [ ] 全部字体设置能否使用主窗口样式表继承
* [ ] 初步美化！
* [ ] 调整layout的位置
* [ ] 初步实现切换

决定以后将**不需要更改的内容**放在头文件里定义，可能会被修改的设置为接口

1. ​    this->hide();//主界面关闭  

2. ​    video *videodlg = new video(this);//新建子界面  

3. ​    connect(videodlg,SIGNAL(sendsignal()),this,SLOT(reshow()));//当点击子界面时，调用主界面的reshow()函数  

4. ​    videodlg->show();//子界面出现  

   **当前版本存在的问题：切换过程通过隐藏和显示不同窗口实现，窗口切换过程中会短暂显示到桌面**

   ==考虑使用stackLayout重新布局==

   ==或者使用qml==

   > setWindowState(Qt::WindowMaximized)

## 4.2日

* [x] 熟悉信号槽使用，信号&槽的定义方式，总结为md文件
* [ ] 初步了解项目窗口切换方式，总结为md文件
* [x] 研究StyleSheet，总结属性
* [x] 将Qt Design生成的.ui文件通过debug的方式转换为代码结构
* [ ] 使用信号槽刷新时间显示
* [x] 了解QT对中文支持不好的原因，总结为md文件
* [ ] 初步了解设计进程交互框架可以使用socket，初步确定使用前后端分离的方式开发