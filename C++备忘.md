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

## 输入数据

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
while(gets(str)); //^Z返回值为0
while((a=getchar())!=EOF);//^Z 返回值为-1
//string::npos == -1;
s.rfind();//返回-1
```

### 字符串中含回车

在这种情况下，如果没有题目的说明，程序无法知道哪里是字符串的分界。那么，用scanf("%c",&ch)来读，一边读，一边判断分界条件是否满足，如果满足，则把当前读到的东西存到一个字符串中。



## 字符串处理

### string转int的方式

1. 采用最原始的string, 然后按照十进制的特点进行算术运算得到int，但是这种方式太麻烦，这里不介绍了。

2. 采用标准库中atoi函数。**atoi**     `在使用的时候一定要将str转换为a[n]的形式`

   `string s = "12"; int a = atoi(s.c_str());` 
   对于其他类型也都有相应的标准库函数，比如浮点型atof(),long型atol()等等。

3. 采用sstream头文件中定义的字符串流对象来实现转换。

   `istringstream is("12"); //构造输入字符串流，流的内容初始化为“12”的字符串 int i; is >> i; //从is流中读入一个int整数存入i中`

### int转string的方式

1. 采用标准库中的to_string函数。**string 类型 to_string**

   `int i = 12; cout << std::to_string(i) << endl;` 
   不需要包含任何头文件，应该是在utility中，但无需包含，直接使用，还定义任何其他内置类型转为string的重载函数，很方便。

2. 采用sstream中定义的字符串流对象来实现。

   `ostringstream os; //构造一个输出字符串流，流内容为空 int i = 12; os << i; //向输出字符串流中输出int整数i的内容 cout << os.str() << endl; //利用字符串流的str函数获取流中的内容` 
   字符串流对象的str函数对于istringstream和ostringstream都适用，都可以获取流中的内容。

   ```c++    
   itoa(i ,num ,10 );//stdlib.h ！！！！！！！****linux下没有！*****！！！！！
   //i ---- 需要转换成字符串的数字
   //num ---- 转换后保存字符串的变量
   //10 ————转换基数
   ```




vector的几种初始化及赋值方式：
（1）不带参数的构造函数初始化

```c++
//初始化一个size为0的vector
vector<int> abc;
```

（2）带参数的构造函数初始化

```c++
//初始化size,但每个元素值为默认值
vector<int> abc(10);    //初始化了10个默认值为0的元素
//初始化size,并且设置初始值
vector<int> cde(10，1);    //初始化了10个值为1的元素
```

（3）通过数组地址初始化

```c++
int a[5] = {1,2,3,4,5};
//通过数组a的地址初始化，注意地址是从0到5（左闭右开区间）
vector<int> b(a, a+5);
```





（4）通过同类型的vector初始化

```c++
vector<int> a(5,1);
//通过a初始化
vector<int> b(a);
```



（5）通过insert初始化

```c++
//insert初始化方式将同类型的迭代器对应的始末区间（左闭右开区间）内的值插入到vector中
vector<int> a(6,6);
vecot<int> b;
//将a[0]~a[2]插入到b中，b.size()由0变为3
b.insert(b.begin(), a.begin(), a.begin() + 3);
```


insert也可通过数组地址区间实现插入

```c++
int a[6] = {6,6,6,6,6,6};
vector<int> b;
//将a的所有元素插入到b中
b.insert(b.begin(), a, a+7);
```

此外，insert还可以插入m个值为n的元素

```c++
//在b开始位置处插入6个6
b.insert(b.begin(), 6, 6);
```



（6）通过copy函数赋值



```c++
vector<int> a(5,1);
int a1[5] = {2,2,2,2,2};
vector<int> b(10);

/*将a中元素全部拷贝到b开始的位置中,注意拷贝的区间为a.begin() ~ a.end()的左闭右开的区间*/
copy(a.begin(), a.end(), b.begin());

//拷贝区间也可以是数组地址构成的区间
copy(a1, a1+5, b.begin() + 5);
```















### 小写字符

```c++
tolower('c');
toupper('c');
```

## DP动态规划问题小结

```c++
// DP问题总结
/****************************************************************
要仔细观察最后一项f(n)的推倒，观察状态的情况
导出递推式的时候一定要严谨，要包含所有情况
注意边界条件的判定，往往使dp[0]=0； dp[1]...之后才是正式的递推过程
*****************************************************************/
// leetcode题解
// 最大子序和
// 股票序列最大收益I
/*
	输入: [7,1,5,3,6,4]
	输出: 5
	解释: 在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。
		 注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格。
*/
// 爬楼梯系列
/*
	输入： 2
	输出： 2
	解释： 有两种方法可以爬到楼顶。
	1.  1 阶 + 1 阶
	2.  2 阶
*/
// 使用最小花费爬楼梯：746
/* 
	输入: cost = [10, 15, 20]
	输出: 15
	解释: 最低花费是从cost[1]开始，然后走两步即可到阶梯顶，一共花费15。
*/
```



## 位运算骚操作

```c++
*str = *str | ('a'-'A'); //大写字符转小写 A=64+1 Z=64+25
```





```c++
*str = *str | ('a'-'A'); //大写字符转小写
```











## 骚操作

* 创建一维数组代替二维，使用宏定义#define X(x) x/num 确定行号
* 

```c++
name.erase(remove(name.begin(),name.end(),'.'),name.end());
```

* 奇偶分块，双指针，一个从前向后，一个从后向前。
* 

```c++
    map<char,int> m;
    int dx[4]={0,0,1,-1};
    int dy[4]={1,-1,0,0};
    bool judgeCircle(string moves) {
        m['R']=2;
        m['L']=3;
        m['U']=0;
        m['D']=1;
        int x=0,y=0;
        for (char a:moves){
            x+=dx[m[a]];
            y+=dy[m[a]];
        }
        if (x==0&&y==0)
            return true;
        return false;
    }
```

* str.erase(remove(str.begin(),str.end(),'a'),str.end());





#### LeetCode 977

给定一个按非递减顺序排序的整数数组 `A`，返回每个数字的平方组成的新数组，要求也按非递减顺序排序。

**示例 1：**

```
输入：[-4,-1,0,3,10]
输出：[0,1,9,16,100]
```

**思路双指针从外向内**

#### LeetCode 929

每封电子邮件都由一个本地名称和一个域名组成，以 @ 符号分隔。

例如，在 `alice@leetcode.com`中， `alice` 是本地名称，而 `leetcode.com` 是域名。

除了小写字母，这些电子邮件还可能包含 `'.'` 或 `'+'`。

如果在电子邮件地址的**本地名称**部分中的某些字符之间添加句点（`'.'`），则发往那里的邮件将会转发到本地名称中没有点的同一地址。例如，`"alice.z@leetcode.com”` 和 `“alicez@leetcode.com”` 会转发到同一电子邮件地址。 （请注意，此规则不适用于域名。）

如果在**本地名称**中添加加号（`'+'`），则会忽略第一个加号后面的所有内容。这允许过滤某些电子邮件，例如 `m.y+name@email.com` 将转发到 `my@email.com`。 （同样，此规则不适用于域名。）

可以同时使用这两个规则。

给定电子邮件列表 `emails`，我们会向列表中的每个地址发送一封电子邮件。实际收到邮件的不同地址有多少？

```c++
class Solution {
public:
    int numUniqueEmails(vector<string>& emails) {
        int res,j;
        string name,domain,temp;
        int len = emails.size();
        set<string> m;
        for (int i=0;i<len;i++){
            temp=emails[i];
            name=temp.substr(0,temp.find('@'));
            name = name.substr(0,name.find('+'));
            name.erase(remove(name.begin(),name.end(),'.'),name.end());
            domain=temp.substr(temp.find('@')+1);
            m.insert(name+"@"+domain);
        }
        return m.size();
    }
};
```

#### LeetCode #6 Z字变换

将一个给定字符串根据给定的行数，以从上往下、从左到右进行 Z 字形排列。

比如输入字符串为 `"LEETCODEISHIRING"` 行数为 3 时，排列如下：

```
L   C   I   R
E T O E S I I G
E   D   H   N
```

之后，你的输出需要从左往右逐行读取，产生出一个新的字符串，比如：`"LCIRETOESIIGEDHN"`。

请你实现这个将字符串进行指定行数变换的函数：

```
string convert(string s, int numRows);
```

```c++
    string convert(string s, int numRows) {
        if(numRows==1) return s;
        vector <string> rows(min(numRows,int(s.size())));
        //
        int cur=0;
        bool godown=0;
        //行号上下碰撞
        for (char c:s){
            rows[cur]+=c;
            if (cur==0||cur==numRows-1) godown=!godown;
            cur+=godown?1:-1;
        }
        
        string res;
        for (string row:rows)res+=row;
        return res;
    }
```



#### LeetCode #8 字符串转整数

请你来实现一个 `atoi` 函数，使其能将字符串转换成整数。

**示例 1:**

```
输入: "42"
输出: 42
```

**示例 2:**

```
输入: "   -42"
输出: -42
解释: 第一个非空白字符为 '-', 它是一个负号。
     我们尽可能将负号与后面所有连续出现的数字组合起来，最后得到 -42 。
```

**示例 3:**

```
输入: "4193 with words"
输出: 4193
解释: 转换截止于数字 '3' ，因为它的下一个字符不为数字。
```

**示例 4:**

```
输入: "words and 987"
输出: 0
解释: 第一个非空字符是 'w', 但它不是数字或正、负号。
     因此无法执行有效的转换。
```

**示例 5:**

```
输入: "-91283472332"
输出: -2147483648
解释: 数字 "-91283472332" 超过 32 位有符号整数范围。 
     因此返回 INT_MIN (−231) 。
```

**在编写时需要综合考虑所有可能情况** **先列可能的情况，再开始编程**

* int最大值 `0x7fffffff`

* int最小值`0x80000000`







#### LeetCode #5 最长回文子串















####  LeetCode 只出现一次的数字

给定一个**非空**整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

**说明：**

**你的算法应该具有线性时间复杂度。 你可以不使用额外空间来实现吗？**

**示例 1:**

```
输入: [2,2,1]
输出: 1
```

**示例 2:**

```
输入: [4,1,2,1,2]
输出: 4
```

```c++
    int singleNumber(vector<int>& nums) {
        int i=0;
        for (auto n:nums){
            i^=n;//按位异或，偶数次相同的数字都会消去
        }
        return i;
    }
```

### 求众数

求众数

给定一个大小为 *n* 的数组，找到其中的众数。众数是指在数组中出现次数**大于** `⌊ n/2 ⌋` 的元素。你可以假设数组是非空的，并且给定的数组总是存在众数。

**示例 1:**

```
输入: [3,2,3]
输出: 3
```

**示例 2:**

```
输入: [2,2,1,1,1,2,2]
输出: 2
```

#### 搜索二维矩阵 II

编写一个高效的算法来搜索 *m* x *n* 矩阵 matrix 中的一个目标值 target。该矩阵具有以下特性：

- 每行的元素从左到右升序排列。
- 每列的元素从上到下升序排列。

**示例:**

现有矩阵 matrix 如下：

```
[
  [1,   4,  7, 11, 15],
  [2,   5,  8, 12, 19],
  [3,   6,  9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30]
]
```

给定 target = `5`，返回 `true`。

给定 target = `20`，返回 `false`。

**分治法。**

- 左下角的元素是这一行中最小的元素，同时又是这一列中最大的元素。比较左下角元素和目标：
  - 若左下角元素等于目标，则找到
  - 若左下角元素大于目标，则目标不可能存在于当前矩阵的最后一行，问题规模可以减小为在去掉最后一行的子矩阵中寻找目标
  - 若左下角元素小于目标，则目标不可能存在于当前矩阵的第一列，问题规模可以减小为在去掉第一列的子矩阵中寻找目标
- 若最后矩阵减小为空，则说明不存在



#### 动态规划

为了改进暴力法，我们首先观察如何避免在验证回文时进行不必要的重复计算。考虑 
$$
\textrm{“ababa”}
$$
 这个示例。如果我们已经知道 
$$
\textrm{“bab”}
$$
是回文，那么很明显，
$$
\textrm{“ababa”}
$$
 一定是回文，因为它的左首字母和右尾字母是相同的。

我们给出 P(i,j)*P*(*i*,*j*) 的定义如下：
$$
P(i,j) = \begin{cases} \text{true,} &\quad\text{如果子串} S_i \dots S_j \text{是回文子串}\\ \text{false,} &\quad\text{其它情况} \end{cases}
$$
因此，
$$
P(i, j) = ( P(i+1, j-1) \text{ and } S_i == S_j )
$$
基本示例如下：
$$
P(i, i) = true
$$

$$
P(i, i+1) = ( S_i == S_{i+1} )
$$

这产生了一个直观的动态规划解法，我们首先初始化一字母和二字母的回文，然后找到所有三字母回文，并依此类推…

**复杂度分析**

- 时间复杂度：O(n^2)*O*(*n*2)， 这里给出我们的运行时间复杂度为 O(n^2)*O*(*n*2) 。
- 空间复杂度：O(n^2)*O*(*n*2)， 该方法使用 O(n^2)*O*(*n*2) 的空间来存储表。

> C++，选取2n-1个中心，然后扩展的方法。

```c++
class Solution {
public:
    string longestPalindrome(string s) {
        if (s.length()<= 1) return s;
        int lenMax= 0, left= 0;
        for (int i= 0; i< s.length(); i++) {
            int len1= expand(s, i, i);
            int len2= expand(s, i, i+ 1);
            int len= (len1> len2)?len1:len2;
            if (len> lenMax) {
                lenMax= len;
                left= i- (lenMax- 1)/ 2;
            }
        }
        return s.substr(left, lenMax);
    }
    
    int expand(string &s, int left, int right) {
        int l= left, r= right;
        while (l>= 0 && r< s.length() && s[l]== s[r]) {
            --l;
            ++r;
        }
        return r- l- 1;
    }
};
```





# Tips

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

## algorithem库

### sort











### reverse



