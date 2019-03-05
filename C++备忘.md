---
typora-root-url: ./pic
---

# C++备忘

[TOC]

[官方cpp手册](http://zh.cppreference.com/w/cpp/language)

[STL标准库](http://www.cplusplus.com/reference/stl/)



## 友元（friend）

类内可以声明把外部X作为友元，从而实现外部函数（类）对私有类的访问



## 联合体Union

占用同一个空间

联合体的大小仅足以保有其最大的数据成员。其他数据成员分配于该最大成员的一部分相同的字节。分配的细节是实现定义的，且从不是最近写入的联合体成员读取是未定义行为。许多编译器作为非标准语言扩展，实现读取联合体不活跃成员的能力。

```c++
union S
{
    std::int32_t n;     // 占用 4 字节
    std::uint16_t s[2]; // 占用 4 字节
    std::uint8_t c;     // 占用 1 字节
};                      // 整个联合体占用 4 字节
 int main()
{
    S s = {0x12345678}; // 初始化首个成员， s.n 现在是活跃成员
    // 于此点，从 s.s 或 s.c 读取是未定义行为
    std::cout << std::hex << "s.n = " << s.n << '\n';
    s.s[0] = 0x0011; // s.s 现在是活跃成员
    // 在此点，从 n 或 c 读取是 UB 但大多数编译器都对其定义
    std::cout << "s.c is now " << +s.c << '\n' // 11 或 00 ，依赖平台
              << "s.n is now " << s.n << '\n'; // 12340011 或 00115678
}
```

若联合体的成员是类且拥有用户定义的构造函数和析构函数，为切换到该活跃成员，通常需要显式析构函数和布置 new ：

```c++
#include <iostream>
#include <string>
#include <vector>
 //要切换类成员活跃状态
union S
{
    std::string str;
    std::vector<int> vec;
    ~S() {} // 需要知道哪个成员为活跃，仅在类联合类中可行
};          // 整个联合体占有 max(sizeof(string), sizeof(vector<int>))
 
int main()
{
    S s = {"Hello, world"};
    // 在此点，从 s.vec 读取是未定义行为
    std::cout << "s.str = " << s.str << '\n';
    s.str.~basic_string<char>();
    new (&s.vec) std::vector<int>;
    // 现在， s.vec 是联合体的活跃成员
    s.vec.push_back(10);
    std::cout << s.vec.size() << '\n';
    s.vec.~vector<int>();
}
```

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



##常指针
###1.常量指针
常量是形容词，指针是名词，以指针为中心的一个偏正结构短语。这样看，常量指针本质是指针，常量修饰它，表示这个指针乃是一个指向常量的指针（变量）。常量指针的使用要注意，指针指向的对象不能通过这个指针来修改，可是仍然可以通过原来的声明修改，也就是说常量指针可以被赋值为变量的地址，之所以叫做常量指针，是限制了通过这个指针修改变量的值。例如：
```c++ 
int a = 5;
const int b = 8;
const int *c = &a; // 这是合法的，非法的是对c的使用
*c = 6; // 非法，但可以这样修改c指向的对象的值：a = 6;
```
###2. 指针常量 
指针是形容词，常量是名词。这回是以常量为中心的一个偏正结构短语。那么，指针常量的本质是一个常量，而用指针修饰它，那么说明这个常量的值应该是一个指针。
指针常量的值是指针，这个值因为是常量，所以不能被赋值。
在C/C++中，指针常量这样声明：
```c++
int a;
int *const b = &a; //const放在指针声明操作符的右侧
```
###3. 指向常量的指针常量
顾名思议，指向常量的指针常量就是一个常量，且它指向的对象也是一个常量。
因为是一个指针常量，那么它指向的对象当然是一个指针对象，而它又指向常量，说明它指向的对象不能变化。
在C/C++中，这么声明：
```c++ 
const int a = 25;
const int * const b = &a;
///then
char *a = "abcde1234";
const char *b = "bcde"; // b是指向常量字符串的指针变量
char *const c = &a;  // c是指向字符指针变量的常量
const char *const d = &b; // d是指向字符常量的指针常量
```
看，指针声明操作符左边有一个const，说明声明的是一个指向常量的指针。再看，指针声明操作符右边有一个const，说明声明的是一个指针常量。前后都锁死了，那么指向的对象不能变，指针常量本身也不能变。细细体味，相信能得其道，下面就不赘述了。
###4.const 成员函数
任何不会修改数据成员的函数都应该声明为const 类型。如果在编写const 成员函数时，不慎修改了数据成员，或者调用了其它非const 成员函数，编译器将指出错误，这无疑会提高程序的健壮性。以下程序中，类stack 的成员函数GetCount 仅用于计数，从逻辑上讲GetCount 应当为const 函数。编译器将指出GetCount 函数中的错误。
```c++ 
class Stack
{
public:
    void Push(int elem);
    int Pop(void);
    int GetCount(void) const; // const 成员函数
private:
    int m_num;
    int m_data[100];
};
int Stack::GetCount(void) const
{
    ++ m_num; // 编译错误，企图修改数据成员m_num
    Pop(); // 编译错误，企图调用非const 函数
    return m_num;
}
```
### 5.用const 修饰函数的返回值
如果给以“指针传递”方式的函数返回值加const 修饰，那么函数返回值（即指针）的内容不能被修改，该返回值只能被赋给加const 修饰的同类型指针。例如函数
```c++
const char * GetString(void);
如下语句将出现编译错误：
char *str = GetString();
正确的用法是
const char *str = GetString();
如果函数返回值采用“值传递方式”，由于函数会把返回值复制到外部临时的存储单元中，加const 修饰没有任何价值。
```
###6.用const 修饰函数的参数
如果参数作输出用，不论它是什么数据类型，也不论它采用“指针传递”还是“引用传递”，都不能加const 修饰，否则该参数将失去输出功能。const 只能修饰输入参数：
如果输入参数采用“指针传递”，那么加const 修饰可以防止意外地改动该指针，起到保护作用。





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



# PAT/ACM

##输入数据

### 一组输入数据

```c++
scanf("%d %d %lld %f %lf",&a,&b,&ll,&Float,&Double);
printf("%d %.4f\n",a,f);
cin>>a>>b;
cout<<a<<" "<<b<<endl;
```

### 多组输入数据，直到读至输入文件末尾为止

```c++
while (scanf("%d %d",&a, &b) != EOF) //
        printf("%d\n",a+b);
while (cin>>a>>b) //如果cin没有输入就是0
        cout<<a+b<<endl;
```

### 多组输入数据，以某特殊输入为结束标志

```c++
while(scanf("%d %d",&a, &b) &&(a||b)) //关键在于&&
         printf("%d\n",a+b);
while(cin>>a>>b&&(a||b)) //关键在于&&
    {cout<<a+b<<endl;}
    return 0;
```

### 多组输入数据，开始输入一个N，下面N组数据

```c++
scanf("%d",n);
while(n--)
    scanf("%d %d",&a,&b);

cin>>n;
while(n--){
    cin>>a>>b;
    cout<<a+b<<endl;
}
```

## 字符串输入

### 每个字符串中不含空格、制表符及回车

```c++
char str1[1000], str2[1000];
scanf("%s%s", str1, str2);//此处可以加空格可以不加
也可以采用 string s;
cin>>s;
```

### 字符串中含有空格、制表符，但不含回车

```c++
char str[1000];
gets(str);
```

### 字符串中含回车

在这种情况下，如果没有题目的说明，程序无法知道哪里是字符串的分界。那么，用scanf("%c",&ch)来读，一边读，一边判断分界条件是否满足，如果满足，则把当前读到的东西存到一个字符串中。



## 字符串处理













#Tips

### 连续赋值原理

```c++
//连续赋值：原因是由于·赋值操作是有返回值的·！返回值就是右值
a=b=(c+d);
```

### 连续输入(回车也读入)

```c++
char a;
//除了eof别的什么都吃
while ((a=getchar()!=-1){//EOF==-1
        
}
```

### foreach循环

```c++
vector<int> v;
for(auto vv:v){ //容器必须支持iterator遍历
    cout<<vv;
}
```

### 自定义比较

```c++
#include <algorithm>
#include <functional>
sort(v.begin(),v.end(),cmp); //bool cmp(int a,int b){return a<b;}
sort(v.begin(),v.end(),less<int>());//定义增加排序
//lambda表达式
sort(v.begin(), v.end(), 
		[](pair<string, int > a, pair<string, int > b)->
		bool {return a.second < b.second; }
	);
```

### 模板类

```c++
//输出的模板类utils
template<typename T>
void printAll(vector<T> t){
    for (T tt:t){
        cout<<tt<<":";
    }
}
```

