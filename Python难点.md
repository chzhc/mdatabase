# Python

## 知识点

### 自省机制

在计算机编程中，**自省**是指这种能力：**检查某些事物以确定它是什么、它知道什么以及它能做什么**。

```python
#coding: UTF-8
import sys #  模块，sys指向这个模块对象
import inspect
def foo(): pass # 函数，foo指向这个函数对象
 
class Cat(object): # 类，Cat指向这个类对象
    def __init__(self, name='kitty'):
        self.name = name
    def sayHi(self): #  实例方法，sayHi指向这个方法对象，使用类或实例.sayHi访问
        print self.name, 'says Hi!' # 访问名为name的字段，使用实例.name访问
 
cat = Cat() # cat是Cat类的实例对象
 
print Cat.sayHi # 使用类名访问实例方法时，方法是未绑定的(unbound)
print cat.sayHi # 使用实例访问实例方法时，方法是绑定的(bound)
```

查看函数对象

```python
In [14]: class a():
    ...:     pass
    ...:
    ...:
#定义一个a类，可以看到变成了__main__函数的对象
In [15]:
Out[15]: __main__.a  

#使用自省获取反射
In [91]: "asd".__len__()
Out[91]: 3
In [92]: len("asd")
Out[92]: 3
```

有时候我们会碰到这样的需求，需要执行对象的某个方法，或是需要对对象的某个字段赋值，而方法名或是字段名在编码代码时并不能确定，需要通过参数传递字符串的形式输入。举个具体的例子：当我们需要实现一个通用的DBM框架时，可能需要对数据对象的字段赋值，但我们无法预知用到这个框架的数据对象都有些什么字段，换言之，我们在写框架的时候需要通过某种机制访问未知的属性。

**这个机制被称为反射，或是自省**，用于实现在运行时获取未知对象的信息。反射是个很吓唬人的名词，听起来高深莫测，在一般的编程语言里反射相对其他概念来说稍显复杂，一般来说都是作为高级主题来讲；但在Python中反射非常简单，用起来几乎感觉不到与其他的代码有区别，使用反射获取到的函数和方法可以像平常一样加上括号直接调用，获取到类后可以直接构造实例；不过获取到的字段不能直接赋值，因为拿到的其实是另一个指向同一个地方的引用，赋值只能改变当前的这个引用而已。

查看对象的属性

- **dir([obj]):** 
  调用这个方法将返回包含obj大多数属性名的列表（会有一些特殊的属性不包含在内）。obj的默认值是当前的模块对象。
- **hasattr(obj, attr):** 这个方法用于检查obj是否有一个名为attr的值的属性，返回一个布尔值。
- **getattr(obj, attr):** 调用这个方法将返回obj中名为attr值的属性的值，例如如果attr为'bar'，则返回obj.bar。
- **setattr(obj, attr, val):** 
  调用这个方法将给obj的名为attr的值的属性赋值为val。例如如果attr为'bar'，则相当于obj.bar = val。

### Python多线程

**(1) python下多线程的限制以及多进程中传递参数的方式**

　　python多线程有个**全局解释器锁**（`global interpreter lock`），这个锁的意思是任一时间只能有一个线程使用解释器，跟单cpu跑多个程序一个意思，大家都是轮着用的，这叫“并发”，不是“并行”。

　　多进程间共享数据，可以使用 `multiprocessing.Value` 和 `multiprocessing.Array`

**(2) python多线程与多进程的区别**　　

- 在UNIX平台上，当某个进程终结之后，该进程需要被其父进程调用wait，否则进程成为僵尸进程(Zombie)。所以，有必要对每个Process对象调用join()方法 (实际上等同于wait)。对于多线程来说，由于只有一个进程，所以不存在此必要性。
- **多进程应该避免共享资源**。在多线程中，我们可以比较容易地共享资源，比如使用全局变量或者传递参数。在多进程情况下，由于每个进程有自己独立的内存空间，以上方法并不合适。此时我们可以通过共享内存和Manager的方法来共享资源。但这样做提高了程序的复杂度，并因为同步的需要而降低了程序的效率。

**多进程模式最大的优点就是稳定性高**，因为一个子进程崩溃了，不会影响主进程和其他子进程。（当然主进程挂了所有进程就全挂了，但是Master进程只负责分配任务，挂掉的概率低）著名的Apache最早就是采用多进程模式。

**多进程模式的缺点是创建进程的代价大**，在Unix/Linux系统下，用`fork`调用还行，在Windows下创建进程开销巨大。另外，操作系统能同时运行的进程数也是有限的，在内存和CPU的限制下，如果有几千个进程同时运行，操作系统连调度都会成问题。

**多线程模式通常比多进程快一点，但是也快不到哪去，而且，多线程模式致命的缺点就是任何一个线程挂掉都可能直接造成整个进程崩溃，因为所有线程共享进程的内存。**



#### 线程同步

使用Thread对象的Lock和Rlock可以实现简单的线程同步，这两个对象都有acquire方法和release方法，对于那些需要每次只允许一个线程操作的数据，可以将其操作放到acquire和release方法之间。

每当一个线程比如"set"要访问共享数据时，必须先获得锁定；如果已经有别的线程比如"print"获得锁定了，那么就让线程"set"暂停，也就是同步阻塞；等到线程"print"访问完毕，释放锁以后，再让线程"set"继续。

**Python通过两个标准库thread和threading提供对线程的支持。thread提供了低级别的、原始的线程以及一个简单的锁。**

> threading 模块提供的其他方法：
>
> - threading.currentThread(): 返回当前的线程变量。
> - threading.enumerate(): 返回一个包含正在运行的线程的list。正在运行指线程启动后、结束前，不包括启动前和终止后的线程。
> - threading.activeCount(): 返回正在运行的线程数量，与len(threading.enumerate())有相同的结果。
>
> 除了使用方法外，线程模块同样提供了Thread类来处理线程，Thread类提供了以下方法:
>
> - **run():** 用以表示线程活动的方法。
>
> - start():
>
>   启动线程活动。
>
>   
>
> - **join([time]):** 等待至线程中止。这阻塞调用线程直至线程的join() 方法被调用中止-正常退出或者抛出未处理的异常-或者是可选的超时发生。
>
> - **isAlive():** 返回线程是否活动的。
>
> - **getName():** 返回线程名。
>
> - **setName():** 设置线程名。

```python
class myThread (threading.Thread):
    def __init__(self, threadID, name, counter):
        threading.Thread.__init__(self)
        self.threadID = threadID    
    def run(self):
            print "Starting " + self.name
           # 获得锁，成功获得锁定后返回True
           # 可选的timeout参数不填时将一直阻塞直到获得锁定
           # 否则超时后将返回False
            threadLock.acquire()
            print_time(self.name, self.counter, 3)
            # 释放锁
            threadLock.release()
threadLock = threading.Lock()
threads = []
 
# 创建新线程
thread1 = myThread(1, "Thread-1", 1)
thread2 = myThread(2, "Thread-2", 2)
 
# 开启新线程
thread1.start()
thread2.start()
 
# 添加线程到线程列表
threads.append(thread1)
threads.append(thread2)
```

#### 线程优先级队列（ Queue）

Python的Queue模块中提供了同步的、线程安全的队列类，包括FIFO（先入先出)队列Queue，LIFO（后入先出）队列LifoQueue，和优先级队列PriorityQueue。这些队列都实现了锁原语，能够在多线程中直接使用。可以使用队列来实现线程间的同步。

###深浅拷贝

标准库中的copy模块提供了两个方法来实现拷贝.一个方法是copy,它返回和参数包含内容一样的对象.

```python
import copy
new_list = copy.copy(existing_list)
```

有些时候,你希望对象中的属性也被复制,可以使用deepcopy方法:

```python
import copy
new_list_of_dicts = copy.deepcopy(existing_list_of_dicts)  
```

当你对一个对象赋值的时候(做为参数传递,或者做为返回值),**Python和Java一样,总是传递原始对象的引用,而不是一个副本**.其它一些语言当赋值的时候总是传递副本.Python从不猜测用户的需求 ,如果你想要一个副本,你必须显式的要求.

Python的行为很简单,迅速,而且一致.然而,如果你需要一个对象拷贝而并没有显式的写出来,会出现问题的,比如:

```python
>>> a = [1, 2, 3]
>>> b = a
>>> b.append(5)
>>> print a, b 
[1, 2, 3, 5] [1, 2, 3, 5]
```

如 果你想修改一个对象,而且想让原始的对象不受影响,那你就需要对象复制.正如本节说的一样,你可以使用copy模块中的两个方法来实现需求.一般的,可以 使用copy.copy,它可以进行对象的浅复制(shallow copy),它复制了对象,但对于对象中的元素,依然使用引用.
浅复制,有时无法获得一个和原来对象完全一致的副本,如果你想修改对象中的元素,不仅仅是对象本身的话:

```python
list_of_lists = [ ['a'], [1, 2], ['z', 23] ]
copy_lol = copy.copy(lists_of_lists) 
copy_lol[1].append('boo') 
print list_of_lists, copy_lol 
[['a'], [1, 2, 'boo'], ['z', 23]] [['a'], [1, 2, 'boo'], ['z', 23]] 
```

  要复制一个别的类型,无论是你自己写的还是使用库中的,使用copy.copy,如果你自己写一个类,没有必要费神去写clone和copy函数,如果你想定义自己的类复制的方式,实现一个`__copy__`,或者`__getstate__`和`__setstate__`.如果你想定义自己类型的`deepcopy`,实现方法`__deepcopy__`. 
注意你不用复制不可修改对象(string,数字,元组),因为你不用担心修改它们.如果你想尝试一下复制,依然会得到原来的.

```python
>>> s = 'cat' 
>>> t = copy.copy(s) 
>>> s is t 
True
>>> s='cat'
>>> a=copy.copy(s)
>>> a='123'
>>> s
'cat'
>>> a
'123'
```

### 可变对象以及不可变对象

在 python 中，`strings`, `tuples`, 和 `numbers` 是不可更改的对象，而 `list`,`dict` 等则是可以修改的对象。

**在Python中没有常量**。甚至内置函数都可以赋予一个新值。甚至`dir()`这样的内置函数都可以被重新定义

不可变类型：变量赋值 a=5 后再赋值 a=10，这里实际是新生成一个 int 值对象 10，再让 a 指向它，而 5 被丢弃，不是改变a的值，相当于新生成了a。

![img](assets/20140902164438953)

可变类型：变量赋值 la=[1,2,3,4] 后再赋值 la[2]=5 则是将 list la 的第三个元素值更改，本身la没有动，只是其内部的一部分值被修改了。

![img](assets/20140902165215796)

#### python 函数的参数传递：

**Python函数参数对于可变对象，函数内对参数的改变会影响到原始对象；对于不可变对象，函数内对参数的改变不会影响到原始参数。原因在于：**

不可变类型：类似 c++ 的值传递，如 整数、字符串、元组。如fun（a），传递的只是a的值，没有影响a对象本身。比如在 fun（a）内部修改 a 的值，只是修改另一个复制的对象，不会影响 a 本身。不可变对象，改变的是函数内变量的指向对象。

可变类型：类似 c++ 的引用传递，如 列表，字典。如 fun（la），则是将 la 真正的传过去，修改后fun外部的la也会受影响，可变对象，参数改变的是可变对象，其内容可以被修改。

### 内存管理

　　Python引用了一个内存池(memory pool)机制，即Pymalloc机制(malloc:n.分配内存)，用于管理对小块内存的申请和释放
**内存池（memory pool）的概念：**
　　当 创建大量消耗小内存的对象时，频繁调用new/malloc会导致大量的内存碎片，致使效率降低。内存池的概念就是预先在内存中申请一定数量的，大小相等 的内存块留作备用，当有新的内存需求时，就先从内存池中分配内存给这个需求，不够了之后再申请新的内存。这样做最显著的优势就是能够减少内存碎片，提升效率。
内存池的实现方式有很多，性能和适用范围也不一样。 
**python中的内存管理机制——Pymalloc：**
　　python中的内存管理机制都有两套实现，一套是针对小对象，就是大小小于256bits时,pymalloc会在内存池中申请内存空间；当大于256bits，则会直接执行new/malloc的行为来申请内存空间。
　　关于释放内存方面，当一个对象的引用计数变为0时，python就会调用它的析构函数。在析构时，也采用了内存池机制，从内存池来的内存会被归还到内存池中，以避免频繁地释放动作。

```python
第3层：最上层，用户对Python对象的直接操作
第1层和第2层：内存池，有Python的接口函数PyMem_Malloc实现-----若请求分配的内存在1~256字节之间就使用内存池管理系统进行分配，调用malloc函数分配内存，但是每次只会分配一块大小为256K的大块内存，不会调用free函数释放内存，将该内存块留在内存池中以便下次使用。
第0层：大内存-----若请求分配的内存大于256K，malloc函数分配内存，free函数释放内存。
第-1，-2层：操作系统进行操作
```

#### **垃圾回收**

　　当Python中的对象越来越多，占据越来越大的内存，启动垃圾回收(garbage collection)，将没用的对象清除。

##### 原理

　　当Python的某个对象的引用计数降为0时，说明没有任何引用指向该对象，该对象就成为要被回收的垃圾。比如某个新建对象，被分配给某个引用，对象的引用计数变为1。如果引用被删除，对象的引用计数为0，那么该对象就可以被垃圾回收。

```python
In [74]: a=[321,123]
In [75]: del a
```

**解析del**

　　del a后，已经没有任何引用指向之前建立的[321,123]，该表引用计数变为0，用户不可能通过任何方式接触或者动用这个对象，当垃圾回收启动时，Python扫描到这个引用计数为0的对象，就将它所占据的内存清空。

**注意**

　　1、垃圾回收时，Python不能进行其它的任务，频繁的垃圾回收将大大降低Python的工作效率；

　　2、Python只会在特定条件下，自动启动垃圾回收（垃圾对象少就没必要回收）

　　3、当Python运行时，会记录其中分配对象(object allocation)和取消分配对象(object deallocation)的次数。当两者的差值高于某个阈值时，垃圾回收才会启动。

```python
In [93]: import gc

In [94]: gc.get_threshold()　　#gc模块中查看阈值的方法
Out[94]: (700, 10, 10)
```

阈值分析：

　　700即是垃圾回收启动的阈值；

　　每10次0代垃圾回收，会配合1次1代的垃圾回收；而每10次1代的垃圾回收，才会有1次的2代垃圾回收；

当然也是可以手动启动垃圾回收：　

```python
In [95]: gc.collect()    #手动启动垃圾回收
Out[95]: 2
```

**分代回收**

分代回收有点类似于java中的三代

　　Python将所有的对象分为0，1，2三代；

　　所有的新建对象都是0代对象；

　　当某一代对象经历过垃圾回收，依然存活，就被归入下一代对象。



**尝试实现内存泄漏**

```
>>> def leakmem():
...     f = fileinfo.FileInfo('/music/_singles/kairo.mp3') 
...     
>>> for i in range(100):
...     leakmem()                                          
```

每次 `leakmem` 函数被调用，我们创建 `FileInfo` 的一个实例，将其赋给变量 `f`，这个变量是函数内的一个局部变量。然后函数结束未曾释放 `f`，所以你可能认为有内存泄漏，但是你错了。当函数结束时，局部变量 `f` 超出了作用域。在这个地方，不再有任何对 `FileInfo` 新创建实例的引用(因为除了 `f` 我们从未将其赋值给其它变量)，所以Python替我们破坏掉实例。

不管我们调用 `leakmem` 函数多少次，决不会泄漏内存，因为每一次，Python将在从 `leakmem` 返回前破坏掉新创建的 `FileInfo` 类。

对于这种垃圾收集的方式，技术上的术语叫做“引用计数”。Python维护着对每一个创建的实例的引用的列表。在上面的例子中，对于 `FileInfo` 实例只有一个引用：局部变量 `f`。当函数结束时，变量 `f` 超出作用域，所以引用计数降为 `0`，则Python自动破坏实例。

在Python的以前版本中，存在引用计数失败的情况，并且Python不能在你后面进行清除。如果你创建两个实例，它们相互引用(例如，双重链表，每一个结点有都一个指向列表中前一个和后一个结点的指针)，任一个实例都不会被自动破坏，因为Python(正确)认为对于每个实例都存在一个引用。**Python 2.0有一种额外的垃圾回收方式，叫做“标记后清除”，它足够聪明，可以正确地清除循环引用。**

### Lambda函数

　　lambda 函数是一个可以接收任意多个参数(包括可选参数)并且返回单个表达式值的函数。 lambda 函数不能包含命令，它们所包含的表达式不能超过一个。不要试图向lambda 函数中塞入太多的东西；如果你需要更复杂的东西，应该定义一个普通函数，然后想让它多长就多长。

```python
>>> def f(x):          
...     return x*2
...     
>>> f(3)
6
>>> g = lambda x: x*2  
>>> g(3)
6
>>> (lambda x: x*2)(3) 
6
```

这是一个 `lambda` 函数，它完成同上面普通函数相同的事情。注意这里的简短的语法；没有小括号， `return` 是默认的，并且函数没有名字，只有将它赋值给变量的变量名。

你甚至可以不将 `lambda` 函数赋值给一个变量而使用它。这不是举世无双的东西，它只是展示了 lambda 函数只是一个内联函数。

### 异常处理

**Python的异常处理和Java基本一致**

捕捉异常可以使用try/except语句。

try/except语句用来检测try语句块中的错误，从而让except语句捕获异常信息并处理。

如果你不想在异常发生时结束你的程序，只需在try里捕获它。

语法：

以下为简单的*try....except...else*的语法：

```python
try:
	<语句>        #运行别的代码
except <名字>：
	<语句>        #如果在try部份引发了'name'异常
except <名字>，<数据>:
	<语句>        #如果引发了'name'异常，获得附加的数据
except: # 将会捕获所有异常
	<语句>
else:
	<语句>        #如果没有异常发生
finally: # 退出try时总会执行
	<语句>    
# 手动抛出异常，类似java一样可以对exception做包装
raise [Exception [, args [, traceback]]]
raise Exception("Invalid level!", level)
```

try的工作原理是，当开始一个try语句后，python就在当前程序的上下文中作标记，这样当异常出现时就可以回到这里，try子句先执行，接下来会发生什么依赖于执行时是否出现异常。

- 如果当try后的语句执行时发生异常，python就跳回到try并执行第一个匹配该异常的except子句，异常处理完毕，控制流就通过整个try语句（除非在处理异常时又引发新的异常）。
- 如果在try后的语句里发生了异常，却没有匹配的except子句，异常将被递交到上层的try，或者到程序的最上层（这样将结束程序，并打印缺省的出错信息）。
- 如果在try子句执行时没有发生异常，python将执行else语句后的语句（如果有else的话），然后控制流通过整个try语句。







### 非常规数据类型



#### 字典

```python
>>> d = {"server":"mpilgrim", "database":"master"}
>>> d
{'server': 'mpilgrim', 'database': 'master'}
>>> d["database"] = "pubs" 
>>> d
{'server': 'mpilgrim', 'database': 'pubs'}
>>> d["uid"] = "sa"    
>>> d
{'server': 'mpilgrim', 'uid': 'sa', 'database': 'pubs'}
```

字典不是只用于字符串。字典的值可以是任意数据类型，包括字符串，整数，对象，或者甚至其它的字典。在一个单个字典里，字典的值并不需要全都是同一数据类型，可以根据需要混用和配匹。

字典的关键字要严格一些，但是它们可以是字符串，整数和几种其它的类型(后面还会谈到这一点)。也可以在一个字典中混用和配匹关键字。不能在一个字典中有重复的键字。给一个存在的键字赋值会抹掉原来的值。

可以在任何时候加入新的键-值对。这种语法同修改存在的值一样。(是的，它可能某天会给你带来麻烦，你可能认为增加了新值，但实际上只是反复地修改了同样的值，因为你的键字没有按照你的想象改变。)

**字典没有元素顺序的概念**。说元素顺序乱了是不正确的，它们只是简单的无序。这是一个重要的特性，它会在你想要以一种特定的，可重复的顺序(象以键字的字母表顺序)存取字典元素的时候骚扰你。有一些实现的方法，它们只是没有加到字典中去。

``` python
del d[`key`] # 允许你根据键字将单个元素从字典中删除。
d.clear()  # 会删除一个字典中所有元素。注意空的大括号所表示的集合说明一个字典没有元素。
```



#### 列表

一个列表是一个用方括号包括的有序元素集。

一个列表可以象一个以0开始的数组一样使用。任何一个非空列表的第一个元素总是 `li[0]`。

这个5元素列表的最后一个元素是 `li[4]`，因为列表总是从0开始。

```python
>>> li = ["a", "b", "mpilgrim", "z", "example"] 
>>> li
['a', 'b', 'mpilgrim', 'z', 'example']
>>> li[0]                                       
'a'
>>> li[4]                                       
'example'
# 过滤列表
>>>>[elem for elem in li if len(elem)>1]
['mpilgrim', 'example']
```

#### 序列

序列是不可变列表。一旦创建了一个序列就不能以任何方式改变它。

```python
>>> t = ("a", "b", "mpilgrim", "z", "example") 
>>> t
('a', 'b', 'mpilgrim', 'z', 'example')
>>> t[0]                                       
'a'
>>> t[-1]                                      
'example'
>>> t[1:3]                                     
('b', 'mpilgrim')
```

序列的元素象列表一样按定义的次序进行排序。序列的索引象列表一样从0开始，所以一个非空序列的第一个元素总是` t[0]`。负数索引象列表一样从序列尾部开始计数。

分片也可以使用，就象列表一样。注意当分割一个列表时，会得到一个新的列表；当分割一个序列时，会得到一个新的序列。

* 你不能向序列增加元素。序列没有 `append` 或 `extend` 方法。
* 你不能从序列中除掉元素。序列没有 `remove` 或` pop` 方法。
* 你不能在序列中查找元素。序列没有 `index` 方法。
* 然而，你可以使用 `in` 来看一看是否一个元素存在于序列中。

**优点**

- 序列比列表操作速度快。如果你定义了一个值集合常量，并且唯一要用它做的是不断地遍历它，使用序列代替列表。
- 记得我说过字典关键字可以是整数，字符串和“几种其它的类型”吗？序列就是那些类型之一。序列可以在字典中被用作关键字，但是列表不行。[[2](http://www.kuqin.com/diveinto_python_document/odbchelper_tuple.html#ftn.c40ab4c10b6b1ab5)]
- 序列用在字符串格式化，这一点我们会很快看到。

#### 切片

```python
>>> li
['a', 'b', 'mpilgrim', 'z', 'example']
>>> li[1:3]  
['b', 'mpilgrim']
>>> li[1:-1] 
['b', 'mpilgrim', 'z']
>>> li[0:3]  
['a', 'b', 'mpilgrim']

```

### 序列赋值

```python
>>> v = ('a', 'b', 'e')
>>> (x, y, z) = v 
>>> x
'a'
>>> y
'b'
>>> z
'e'
```

它有着许多的用处。当构建可重用的模块时，你经常需要给一个名字赋以一系列的值。在C或C++中，你将使用 `enum` 并且手工地列出每个常量和它所对应的值，当值是连续的时候显得特别烦琐。在Python中，你可以使用内置的 `range` 函数来迅速地给多个变量赋予连续值。

```python
>>> range(7)                                                                    
[0, 1, 2, 3, 4, 5, 6]
>>> (MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY) = range(7) 
```

还可以从**函数中返回多个返回值**

``` python
def a():
    v=1
    a=2
    return a,v
```

**遍历字典**

```
>>> for k, v in os.environ.items()  
...     print "%s=%s" % (k, v)
```

**还可以用于列表生成式**

```
>>> print "\n".join(["%s=%s" % (k, v) for k, v in os.environ.items()]) 
```

**还可以直接用来遍历**

```python
di = {'1':('a','b'),'2':('c','d'),'3':('e','f')}
for key,(fir,sec) in di.items():
    print(str(key)+fir+","+sec)
```



### 私有函数

简单来说就是`__fun()`以两个下划线开头但是没有结束的就是私有函数

象大多数语言，Python也有私有函数的概念，私有函数不可以从它们的模块外面被调用；私有类方法，不能够从它们的类外面被调用；并且私有属性，不能够从它们的类外面被使用。不象大多数的语言，一个Python函数，方法，或属性是私有还是公有，完全取决于它的名字。

在 `MP3FileInfo` 中，有两个方法：`__parse` 和 `__setitem__`。正如我们已经讨论过的，`__setitem__` 是一个[专有方法](http://www.kuqin.com/diveinto_python_document/fileinfo_specialmethods.html#fileinfo.specialmethods.setitem.example)；通常，你不直接调用它，而是通过在一个类上使用字典语法来调用，但它是公有的，并且如果有一个真正好的理由，你可以直接调用它(甚至从 `fileinfo` 模块的外面)。然而，`__parse` 是私有的，因为在它的名字前面有两个下划线。

* Python没有类方法保护的概念(只能用于它们自已的类和子父中)。**类方法要不私有(只能在它们自已的类中使用)要不公有(任何地方都可使用)。**
* 在Python中，所有的专用方法(象[`__setitem__`](http://www.kuqin.com/diveinto_python_document/fileinfo_specialmethods.html#fileinfo.specialmethods.setitem.example))和内置属性(象[`__doc__`](http://www.kuqin.com/diveinto_python_document/odbchelper_objects.html#odbchelper.import))遵守一个标准的命名习惯：开始和结束都有两个下划线。**不要对你自已的方法和属性用这种方法命名**；它后面只会搞乱你(或其它人)。

```python
In [152]: class A:
     ...:     def __pri():
     ...:         print("i am private")
     ...:
In [153]: m=A()
In [154]: m.__pri()
---------------------------------------------------------------------------
AttributeError                            Traceback (most recent call last)
AttributeError: 'A' object has no attribute '__pri'
In [155]: dir(m)
Out[155]:
['_A__pri', #这里可以看到存在这个函数，但是私有化了
 '__class__',
 '__delattr__',
 '__dict__',
 ...
 #强制调用会出错
 In [156]: m._A__pri()
---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
TypeError: __pri() takes 0 positional arguments but 1 was given
 
```

如果你试图调用一个私有方法，Python将引发一个有些误导的异常，宣称那个方法不存在。当然它确实存在，但是它是私有的，所以在类外是不可使用的。

### 类属性

``` python
In [165]: class counter:
     ...:     count = 0
     ...:     def __init__(self):
     ...:         self.__class__.count += 1
In [166]: counter.count
Out[166]: 0
In [167]: a=counter()
In [168]: counter.count
Out[168]: 1
# 由于没有定义删除之后的操作
In [169]: del a
In [170]: counter.count
Out[170]: 1
```

主要内容来自[Dive into python](http://www.kuqin.com/diveinto_python_document/toc.html)



## Python的具体细节

#### 读入大文件

读取文件，最常见的方式是：

```python
with open('filename', 'r', encoding = 'utf-8') as f:
    for line in f.readlines():
        do_something(line)
```

但是，当完成这一操作时，`readlines()` 方法（`read()` 也一样）会将整个文件加载到内存中。在文件较大时，往往会引发 `MemoryError`（内存溢出）。

**最应该采用的方式是**

```python
with open('filename', 'r', encoding = 'utf-8') as f:
    for line in f:
        do_something(line)
```

`with` 语句句柄负责打开和关闭文件（包括在内部块中引发异常时），`for line in f` 将文件对象 `f` 视为一个可迭代的数据类型，会自动使用 `IO` 缓存和内存管理，这样就不必担心大文件了。

#### Python生成随机数

import random
random.random()
它会返回一个随机的0和1之间的浮点数

#### Python中pass语句的作用

pass语句什么也不做，一般作为占位符或者创建占位程序，pass语句不会执行任何操作

``` python
函数                      描述
int(x [,base ])         将x转换为一个整数
float(x )               将x转换到一个浮点数
complex(real [,imag ])  创建一个复数
str(x )                 将对象 x 转换为字符串
repr(x )                将对象 x 转换为表达式字符串
eval(str )              用来计算在字符串中的有效Python表达式,并返回一个对象
tuple(s )               将序列 s 转换为一个元组
list(s )                将序列 s 转换为一个列表
chr(x )                 将一个整数转换为一个字符
ord(x )                 将一个字符转换为它的整数值
hex(x )                 将一个整数转换为一个十六进制字符串
oct(x )                 将一个整数转换为一个八进制字符串
```

#### python的bug和进行静态的代码分析工具

> pycheck pylint 

#### 在一个function里设置全局的变量

> global

#### 用Python删除一个文件

**os库比较常用，可以用来执行脚本，或者路径操作**

使用os.remove(filename)或者os.unlink(filename);

#### Python程序中文输出问题

`python2`中中文支持不好，`python3`中字符串默认采用`unicode`的方式，首先可以将

```python 
s=requests.get('http://qq.ip138.com/weather/zhejiang/HangZhou/201401.htm')
demo=response.text
# 应该直接就是字节流，python输出的时候用默认的方式进行字节流转换了应该，将其encode到unicode==`iso-8859-1` 然后解码可用（网页采用gb2312编码）
s.encode('iso-8859-1').decode('gb2312')
```

用`encode`和`decode`

#### Python代码得到列表list的交集与差集

巧用列表生成式

交集

```python
b1=[1,2,3]
b2=[2,3,4]
b3 = [val for val in b1 if val in b2]
print b3
```

差集

```python
b1=[1,2,3]
b2=[2,3,4]
b3 = [val for val in b1 if val not in b2]
print b3 
```

#### 写一个简单的python socket编程

```python
# Server
socket = socket.socket( family, type )#创建socket对象

socket.bind( address ) # 将socket绑定到指定地址
socket.listen( backlog ) # 使用socket套接字的listen方法接收连接请求
connection, address = socket.accept() # 服务器套接字通过socket的accept方法等待客户请求一个连接
'''建立完连接之后 服务器和客户端通过send和recv方法通信'''
'''传输结束，服务器调用socket的close方法关闭连接。'''
# Client
socket = socket.socket( family, type )
socket.connect( (host,port) )
'''处理阶段，客户和服务器将通过send方法和recv方法通信。'''
```

​	family参数代表地址家族，可为AF_INET或AF_UNIX。AF_INET家族包括Internet地址，AF_UNIX家族用于同一台机器上的进程间通信。type参数代表套接字类型，可为SOCK_STREAM(流套接字)和SOCK_DGRAM(数据报套接字)。







### Python2和Python3区别

**1.性能** 

Py3.0运行 pystone benchmark的速度比Py2.5慢30%。Guido认为Py3.0有极大的优化空间，在字符串和整形操作上可以取得很好的优化结果。 

Py3.1性能比Py2.5慢15%，还有很大的提升空间。 

**2.编码** Py3.X源码文件默认使用utf-8编码，这就使得以下代码是合法的： 

```
>>> 中国 = 'china' 
>>>print(中国) 
china 
```
**3. 语法** 

1）去除了<>，全部改用!= 

2）去除``，全部改用repr() 

3）关键词加入as 和with，还有True,False,None 

4）整型除法返回浮点数，要得到整型结果，请使用// 

5）加入nonlocal语句。使用noclocal x可以直接指派外围（非全局）变量 

6）去除print语句，加入print()函数实现相同的功能。同样的还有 exec语句，已经改为exec()函数 

7）改变了顺序操作符的行为，例如x<y，当x和y类型不匹配时抛出TypeError而不是返回随即的 bool值 

8）输入函数改变了，删除了raw_input，用input代替： 

```
2.X:guess = int(raw_input('Enter an integer : ')) # 读取键盘输入的方法 
3.X:guess = int(input('Enter an integer : '))
```

9）去除元组参数解包。不能def(a, (b, c)):pass这样定义函数了 

10）新式的8进制字变量，相应地修改了oct()函数。  

```
2.X的方式如下： 
>>> 0666 
438 
>>> oct(438) 
'0666' 
3.X这样： 
>>> 0666 
SyntaxError: invalid token (<pyshell#63>, line 1) 
>>> 0o666 
438 
>>> oct(438) 
'0o666' 
```

11）增加了 2进制字面量和bin()函数 

```
>>> bin(438) 
'0b110110110' 
>>> _438 = '0b110110110' 
>>> _438 
'0b110110110' 
```
12）扩展的可迭代解包。在Py3.X 里，`a, b, *rest = seq`和 `*rest, a = seq`都是合法的，只要求两点：rest是list 对象和seq是可迭代的。

13）新的super()，可以不再给super()传参数， 

14）新的metaclass语法： 
```python
class Foo(*bases, **kwds): 
      pass 
```
15）支持class decorator。用法与函数decorator一样： 

```
    >>> def foo(cls_a): 
          def print_func(self): 
             print('Hello, world!') 
          cls_a.print = print_func 
          return cls_a 
    >>> @foo 
    class C(object): 
      pass 
    >>> C().print() 
    Hello, world! 
```
class decorator可以用来玩玩狸猫换太子的大把戏。更多请参阅PEP 3129 



**4. 字符串和字节串** 

1）现在字符串只有str一种类型，但它跟2.x版本的unicode几乎一样。

2）关于字节串，请参阅“数据类型”的第2条目 

**5.数据类型** 

1）Py3.X去除了long类型，现在只有一种整型——int，但它的行为就像2.X版本的long 

2）新增了bytes类型，对应于2.X版本的八位串，定义一个bytes字面量的方法如下： 
```
>>> b = b'china' 
>>> type(b) 
<type 'bytes'> 
```
str对象和bytes对象可以使用.encode() (str -> bytes) or .decode() (bytes -> str)方法相互转化。 
```    
>>> s = b.decode() 
>>> s 
'china' 
>>> b1 = s.encode() 
>>> b1 
b'china' 
```
3）dict的.keys()、.items 和.values()方法返回迭代器，而之前的iterkeys()等函数都被废弃。同时去掉的还有 
dict.has_key()，用 in替代它吧 

**6.面向对象** 1）引入抽象基类（Abstraact Base Classes，ABCs）。 
2）容器类和迭代器类被ABCs化，所以cellections模块里的类型比Py2.5多了很多。 
```
>>> import collections 
​    >>> print('\n'.join(dir(collections))) 
​    Callable 
​    Container 
​    Hashable 
​    ItemsView 
​    Iterable 
​    Iterator 
​    KeysView 
​    Mapping 
​    MappingView 
​    MutableMapping 
​    MutableSequence 
​    MutableSet 
​    NamedTuple 
​    Sequence 
​    Set 
​    Sized 
​    ValuesView 
​    __all__ 
​    __builtins__ 
​    __doc__ 
​    __file__ 
​    __name__ 
​    _abcoll 
​    _itemgetter 
​    _sys 
​    defaultdict 
​    deque 
```
另外，数值类型也被ABCs化。关于这两点，请参阅 PEP 3119和PEP 3141。 
3）迭代器的next()方法改名为__next__()，并增加内置函数next()，用以调用迭代器的__next__()方法 
4）增加了@abstractmethod和 @abstractproperty两个 decorator，编写抽象方法（属性）更加方便。 


