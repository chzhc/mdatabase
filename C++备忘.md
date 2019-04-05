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

### String转Int的方式

1. 采用最原始的string, 然后按照十进制的特点进行算术运算得到int，但是这种方式太麻烦，这里不介绍了。

2. 采用标准库中atoi函数。**atoi**     `在使用的时候一定要将str转换为a[n]的形式`

   `string s = "12"; int a = atoi(s.c_str());` 
   对于其他类型也都有相应的标准库函数，比如浮点型atof(),long型atol()等等。

3. 采用sstream头文件中定义的字符串流对象来实现转换。

   `istringstream is("12"); //构造输入字符串流，流的内容初始化为“12”的字符串 int i; is >> i; //从is流中读入一个int整数存入i中`

```c++
istringstream is("12"); //构造输入字符串流，流的内容初始化为“12”的字符串   
int i;   
is >> i; //从is流中读入一个int整数存入i中  
```



### Int转String的方式

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

   ```c++
   int aa = 30;
   stringstream ss;
   ss<<aa; 
   string s1 = ss.str();
   cout<<s1<<endl; // 30
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



#### [49. 字母异位词分组](https://leetcode-cn.com/problems/group-anagrams/)

```c++
输入: ["eat", "tea", "tan", "ate", "nat", "bat"],
输出:
[
  ["ate","eat","tea"],
  ["nat","tan"],
  ["bat"]
]

/*
1、质数相乘保证唯一性（字符太长导致longlong溢出）
2、使用字符串hash，将string sort之后作为key排序，value是一个vector列表
使用map[ordered_str].push_back(str) 插入
*/
vector<vector<string>> groupAnagrams(vector<string>& strs) {
        map<string, vector<string>> mm;
        vector<vector<string>> res;
        // int m[26]={0};
        // int curr=2;
        // calc primer
        // for (int i=0;i<26;i++){
        //     for(int ii=curr;;ii++){
        //         bool is=true;
        //         for (int j=2;j<=ii/2;j++){
        //             if (ii%j==0)is=false;
        //         }
        //         if (is){
        //             curr=ii;
        //             break;
        //         }
        //     }
        //     m[i]=curr++;
        // }
        // vector <long long> lonSum;
        //
        for (auto s:strs){
            // long long sum=1;
            string ss=s;
            sort(ss.begin(),ss.end());
            mm[ss].push_back(s);
        }
        //
        for(auto it=mm.begin();it!=mm.end();it++){
            res.push_back(it->second);
        }
        return res;
    }
```

#### [957. N 天后的牢房](https://leetcode-cn.com/problems/prison-cells-after-n-days/)

```c++
输入：cells = [0,1,0,1,1,0,0,1], N = 7
输出：[0,0,1,1,0,0,0,0]
解释：
下表概述了监狱每天的状况：
Day 0: [0, 1, 0, 1, 1, 0, 0, 1]
Day 1: [0, 1, 1, 0, 0, 0, 0, 0]
Day 2: [0, 0, 0, 0, 1, 1, 1, 0]
Day 3: [0, 1, 1, 0, 0, 1, 0, 0]
Day 4: [0, 0, 0, 0, 0, 1, 0, 0]
Day 5: [0, 1, 1, 1, 0, 1, 0, 0]
Day 6: [0, 0, 1, 0, 1, 1, 0, 0]
Day 7: [0, 0, 1, 1, 0, 0, 0, 0]
/*
1、按照规则做
2、位操作~(n<<1 ^ n>>1) 左右同或
*/
    vector<int> prisonAfterNDays(vector<int>& cells, int N) {
		unsigned char bcell=0;
        for (int i=0;i<8;i++){
            if (cells[i]==1){
                bcell=bcell|1<<i;
            }
        }
        vector<unsigned char> vec;
        bcell=(~((bcell>>1)^(bcell<<1)))&126;
        vec.push_back(bcell);
        while(1){
            bcell=(~((bcell>>1)^(bcell<<1)))&126;
            if (vec[0]==bcell){
                 break;
            }
            vec.push_back(bcell);
        }
       
        vector<int> ret(8,0);
        for (int i = 0; i < 8; ++i)
        {
            unsigned char c=vec[(N-1)%vec.size()];
          if (c & (1 << i))
            ret[i] = 1;
        }
        return ret;
    }
```













#### [705. 设计哈希集合](https://leetcode-cn.com/problems/design-hashset/)

```c++
MyHashSet hashSet = new MyHashSet();
hashSet.add(1);         
hashSet.add(2);         
hashSet.contains(1);    // 返回 true
hashSet.contains(3);    // 返回 false (未找到)
hashSet.add(2);          
hashSet.contains(2);    // 返回 true
hashSet.remove(2);          
hashSet.contains(2);    // 返回  false (已经被删除)
/*
所有的值都在 [1, 1000000]的范围内。
操作的总数目在[1, 10000]范围内。
不要使用内建的哈希集合库。
1、使用1000000个桶，通过key值作为寻址
2、Hash开，桶散列，
*/

```

#### [7. 整数反转](https://leetcode-cn.com/problems/reverse-integer/)

```c++
输入: 123
输出: 321
输入: -123
输出: -321
//假设我们的环境只能存储得下 32 位的有符号整数，则其数值范围为 [−231,  231 − 1]。请根据这个假设，如果反转后整数溢出那么就返回 0。
    // 使用字符串流，同时采用long long 避免溢出
    int reverse(int x) {
        string s;
        stringstream ss;
        if (x > 0) {
            while (x) {
                s += '0' + x % 10;
                x /= 10;
            }
        }
        else {
            s += "-";
            while (x) {
                s += '0'+ abs(x % 10);
                x /= 10;
            }
        }
        ss << s;
        long long a;
        ss >> a;
        if (a > 0x7fffffff || a < (int)0x80000000)
            return 0;
        return a;
    }
```













#### [915. 分割数组](https://leetcode-cn.com/problems/partition-array-into-disjoint-intervals/)

```c++
*输入：[5,0,3,8,6]
输出：3
解释：left = [5,0,3]，right = [8,6]
*输入：[1,1,1,0,6,2]
输出：4
解释：left = [1,1,1,0]，right = [6,12]

/* 
1、保存分割点左右的最大值以及位置，4个stack做，左边2个stack，一个保存序列，一个保存最大值
*/
int partitionDisjoint(vector<int>& A) {
        if (A.size()==0)
            return 0;
        int l=0,r=A.size()-1;
        stack<int> sl,sr;
        stack<int> slm,srm;
        for (int i=A.size()-1;i>0;i--){
            sr.push(A[i]);
            if (srm.size()==0||srm.top()>=A[i]){
                srm.push(A[i]);
            }
        }
        sl.push(A[0]);
        slm.push(A[0]);
        while (sr.size()&&slm.top()>srm.top()){
            sl.push(sr.top());
            if (sr.top()>slm.top())
                slm.push(sr.top());
            if (sr.top()==srm.top())
                srm.pop();
            sr.pop();
        }
        return sl.size();
    }
```





#### [645. 错误的集合](https://leetcode-cn.com/problems/set-mismatch/)

集合 `S` 包含从1到 `n` 的整数。不幸的是，因为数据错误，导致集合里面某一个元素复制了成了集合里面的另外一个元素的值，导致集合丢失了一个整数并且有一个元素重复。

给定一个数组 `nums` 代表了集合 `S` 发生错误后的结果。你的任务是首先寻找到重复出现的整数，再找到丢失的整数，将它们以数组的形式返回。

**示例 1:**

```c++
输入: nums = [1,2,2,4]
输出: [2,3]
// 分桶hash
vector<int> findErrorNums(vector<int>& nums) {
        int m[100001] = { 0 };
        int len = nums.size();
        for (auto n : nums) {
            m[n]++;
        }

        int a1, a2;
        for (int i = 1; i <= len; i++) {
            if (m[i] == 0)
                a2 = i;
            else if (m[i] == 2)
                a1 = i;
        }

        vector <int> res = { a1,a2 };
        return res;
    }
```









#### [762. 二进制表示中质数个计算置位](https://leetcode-cn.com/problems/prime-number-of-set-bits-in-binary-representation/)

给定两个整数 `L` 和 `R` ，找到闭区间 `[L, R]` 范围内，计算置位位数为质数的整数个数。

（注意，计算置位代表二进制表示中1的个数。例如 `21` 的二进制表示 `10101` 有 3 个计算置位。还有，1 不是质数。）

**注意:**

1. `L, R` 是 `L <= R` 且在 `[1, 10^6]` 中的整数。
2. `R - L` 的最大值为 10000。

**示例 1:**

```c++
输入: L = 6, R = 10
输出: 4
解释:
6 -> 110 (2 个计算置位，2 是质数)
7 -> 111 (3 个计算置位，3 是质数)
9 -> 1001 (2 个计算置位，2 是质数)
10-> 1010 (2 个计算置位，2 是质数)
    // 解题思路 按照最多的bit位置，建立hash，然后根据setBit数量查找是否质数。
    //int zhishu[] = {2,3,5,7,11,13,17,19};       //10的6次方 对应2的20次方 所以不用统计太多质数
    int countPrimeSetBits(int L, int R) {
        map<int ,bool> m;
        bool flag=true;
        int temp=R,count=0;
        while(temp){
            count++;
            temp/=2;
        }
        for (int i=2;i<count;i++){
            flag=1;
            for (int j=2;j<=i/2;j++){
                if (i%j==0)
                {
                    flag=0;break;
                }
            }
            if (flag)
                m[i]=1;
        }
        int countAll=0;
        for (int i=L;i<=R;i++){
            int setBit=0;
            int ttemp=i;
            while (ttemp){
                if (ttemp&1)
                    setBit++;
                ttemp>>=1;
            }
            if (m[setBit]==1)
                countAll++;
        }
        return countAll;
    }
```

### 动态规划

#### [397. 整数替换](https://leetcode-cn.com/problems/integer-replacement/)

给定一个正整数 *n*，你可以做如下操作：

\1. 如果 *n* 是偶数，则用 `n / 2`替换 *n*。
\2. 如果 *n* 是奇数，则可以用 `n + 1`或`n - 1`替换 *n*。
*n* 变为 1 所需的最小替换次数是多少？

**示例 1:**

```c++
输入:
8
输出:
3
解释:
8 -> 4 -> 2 -> 1
输入:
7
输出:
4
解释:
7 -> 8 -> 4 -> 2 -> 1
或
7 -> 6 -> 3 -> 2 -> 1
    //第一直觉用动态规划，写完之后发现n太大的时候分配空间占据的时间会更多
    //所以采用递归的方式调用，由于/2的话不超过32层，所以不会出现堆栈溢出
    
    int integerReplacement(int n) {
        if (n==INT_MAX)
            return 32;
        if(n == 1)
            return 0;
        if (n%2==0)
            return integerReplacement(n/2)+1;
        else {
            int l,r;
            r=integerReplacement(n+1)+1;
            l=integerReplacement(n-1)+1;
            return min(r,l);
        }
    }
    
```

#### [292. Nim游戏](https://leetcode-cn.com/problems/nim-game/)

你和你的朋友，两个人一起玩 [Nim游戏](https://baike.baidu.com/item/Nim%E6%B8%B8%E6%88%8F/6737105)：桌子上有一堆石头，每次你们轮流拿掉 1 - 3 块石头。 拿掉最后一块石头的人就是获胜者。你作为先手。

你们是聪明人，每一步都是最优解。 编写一个函数，来判断你是否可以在给定石头数量的情况下赢得游戏。

**示例:**

```c++
输入: 4
输出: false 
解释: 如果堆中有 4 块石头，那么你永远不会赢得比赛；
     因为无论你拿走 1 块、2 块 还是 3 块石头，最后一块石头总是会被你的朋友拿走。
     bool canWinNim(int n) {
        if (n<4)
            return 1;
        //本质上可以理解为一个dp问题，如果拿的几个选项中都是1，那就说明我拿完之后对方一定会赢
        //如果拿的几个选项中存在0，我就可以想办法让对方一定拿到0，也就是输
        //！注意如果dp很深呢，例如本题测试用例1348820612，空间分配会出错。
        // int *dp=new int[n+1];
        // dp[0]=1;
        // dp[1]=1;
        // dp[2]=1;
        // dp[3]=1;
        
        // for(int i=4;i<=n;i++){
        //     dp[i]=!(dp[i-1]&&dp[i-2]&&dp[i-3]);//三个不管怎么选都会输的情况为0
        // }
        return n%4!=0;
    }
```



#### [55. 跳跃游戏](https://leetcode-cn.com/problems/jump-game/)

给定一个非负整数数组，你最初位于数组的第一个位置。

数组中的每个元素代表你在该位置可以跳跃的最大长度。

判断你是否能够到达最后一个位置。

**示例 1:**

```
输入: [2,3,1,1,4]
输出: true
解释: 从位置 0 到 1 跳 1 步, 然后跳 3 步到达最后一个位置。
```

**示例 2:**

```c++
输入: [3,2,1,0,4]
输出: false
解释: 无论怎样，你总会到达索引为 3 的位置。但该位置的最大跳跃长度是 0 ， 所以你永远不可能到达最后一个位置。
//在思考的时候自然而然想到，一定要从最后开始往前推，从前往后的范围太广比如9,0,2,1,2,7需要全部试探，
//而在从后往前的时候，只要考虑一个能够到最后一个节点的节点即可，一开始准备使用栈做，将所有可以访问最后一个节点的全部压栈，然后依次弹栈检验。
//优化做法是由于是运动范围，所以只需要使用最近的可达即可，因为如果跳过最近可达可以到达终点的必然可以到达最近可达。
    bool canJump(vector<int>& nums) {
        int len=nums.size();
        int n=len-1;
        for (int i=len-1;i>=0;i--){
            if (i+nums[i]>=n) n=i;
        }
        return n==0;
    }
```



#### [860. 柠檬水找零](https://leetcode-cn.com/problems/lemonade-change/)

在柠檬水摊上，每一杯柠檬水的售价为 `5` 美元。

顾客排队购买你的产品，（按账单 `bills` 支付的顺序）一次购买一杯。

每位顾客只买一杯柠檬水，然后向你付 `5` 美元、`10` 美元或 `20` 美元。你必须给每个顾客正确找零，也就是说净交易是每位顾客向你支付 `5` 美元。

注意，一开始你手头没有任何零钱。

如果你能给每位顾客正确找零，返回 `true` ，否则返回 `false` 。

**示例 1：**

```c++
输入：[5,5,5,10,20]
输出：true
解释：
前 3 位顾客那里，我们按顺序收取 3 张 5 美元的钞票。
第 4 位顾客那里，我们收取一张 10 美元的钞票，并返还 5 美元。
第 5 位顾客那里，我们找还一张 10 美元的钞票和一张 5 美元的钞票。
由于所有客户都得到了正确的找零，所以我们输出 true。
//第一直觉，排序之后，收到+，用掉-，优先使用10来找零。结果出错，要求按照顺序找零！
	bool lemonadeChange(vector<int>& bills) {
        int a=0,b=0,c=0;
        for (auto pay:bills){
            if (pay==5) a++;
            if (pay==10){
                a--;b++;
            }
            if (pay==20){
                c++;
                if (b) b--,a--;
                else a-=3;
            }
            if (a<0||b<0) return 0;
        }
        return 1;
    }
```



#### [452. 用最少数量的箭引爆气球](https://leetcode-cn.com/problems/minimum-number-of-arrows-to-burst-balloons/)

在二维空间中有许多球形的气球。对于每个气球，提供的输入是水平方向上，气球直径的开始和结束坐标。由于它是水平的，所以y坐标并不重要，因此只要知道开始和结束的x坐标就足够了。开始坐标总是小于结束坐标。平面内最多存在104个气球。

一支弓箭可以沿着x轴从不同点完全垂直地射出。在坐标x处射出一支箭，若有一个气球的直径的开始和结束坐标为 xstart，xend， 且满足  xstart ≤ x ≤ xend，则该气球会被引爆。可以射出的弓箭的数量没有限制。 弓箭一旦被射出之后，可以无限地前进。我们想找到使得所有气球全部被引爆，所需的弓箭的最小数量。

**Example:**

```c++
输入:
[[10,16], [2,8], [1,6], [7,12]]

输出:
2

解释:
对于该样例，我们可以在x = 6（射爆[2,8],[1,6]两个气球）和 x = 11（射爆另外两个气球）。
//首先第一个就是排序，然后检查重叠区域，
    int findMinArrowShots(vector<pair<int, int>>& points) 
    {
        //1.对start进行排序
        sort(points.begin(), points.end(), [](const pair<int, int> &lhs, const pair<int, int> &rhs){
            return lhs.first < rhs.first;
        });
        
        //2.计算多个重叠的区间
        int start = INT_MIN, end = INT_MAX, ans = 0;
        for(const auto &p : points)
        {
            //1.发生重叠的情况, 则缩小可射击的右边界, 注意点, 当 start == end 的时候不要射击, 
            //  因为下一个区间能加入的说不定start 是相同的。
            if(p.first <= end && start <= end)
            {
                end = min(end, p.second);
            }
            //2.没有发生重叠的情况, 则需要再射击一次
            else
            {
                ++ans;
                end = p.second;
            }
            //左边的区间是一定会缩减的
            start = p.first;
        }
        return points.size() == 0 ? ans : ans + 1;  // + 1的情况是最后一个重叠的区间没有发射
    }
```




















### 位运算

#### [231. 2的幂](https://leetcode-cn.com/problems/power-of-two/)

```c++
输入: 1
输出: true
解释: 20 = 1
    bool isPowerOfTwo(int n) {
        return (n>0&&!(n&n-1));
    }
```



#### [342. 4的幂](https://leetcode-cn.com/problems/power-of-four/)

```c++
输入: 16
输出: true
bool isPowerOfFour(int num) {
    // return (num>0&&((num|0x55555555)==0x55555555)&&!(num&num-1)||num==1);
    if (num < 0 || num & (num-1)){//check(is or not) a power of 2.
        return false;
    }
    return num & 0x55555555;//check 1 on odd bits
}
```



#### [326. 3的幂](https://leetcode-cn.com/problems/power-of-three/)

```c++
输入: 27
输出: true
    //思路1 质因子，用最大的3^n_max/n 能够除尽就是。
    //思路2 log 后round取整 pow 如果相等就是 
    bool isPowerOfThree(int n) {
        if (n<=0)
            return false;
        int res= (int)(log(n) / log(3)+0.5);
	    return pow(3,res)==n;
    }
```











#### [190. 颠倒二进制位](https://leetcode-cn.com/problems/reverse-bits/)

```c++
输入: 00000010100101000001111010011100
输出: 00111001011110000010100101000000
解释: 输入的二进制串 00000010100101000001111010011100 表示无符号整数 43261596，
      因此返回 964176192，其二进制表示形式为 00111001011110000010100101000000。
    uint32_t reverseBits(uint32_t n) {
        uint32_t res=0;
        for (int i=0;i<32;i++){
            res<<=1;
            res|=n&1;
            n>>=1;

        }
        return res;
    }
```





### 水塘抽样

#### [382. 链表随机节点](https://leetcode-cn.com/problems/linked-list-random-node/)

```c++
// 初始化一个单链表 [1,2,3].
ListNode head = new ListNode(1);
head.next = new ListNode(2);
head.next.next = new ListNode(3);
Solution solution = new Solution(head);

// getRandom()方法应随机返回1,2,3中的一个，保证每个元素被返回的概率相等。
solution.getRandom();
int getRandom() {
        // // srand(time(NULL));
        // ListNode *p;
        // int ind=rand()%len;
        // if (ind>mid){
        //     p=pmid;
        //     ind-=mid;
        // }
        // else {
        //     p=head;
        // }
        // while(ind-->0){
        //     p=p->next;
        // }
        // return p->val;
        ListNode *p;
        p=head;
        bool shoot=false;
        int res=0,llen=len;
        while(1){
            int ind=rand()%llen;
            if (ind==0){
                return p->val;
            }
            p=p->next;
            llen--;
        }
    }
/** 水塘抽样 ——正序：抽到就返回，逆序：直到结束为止
取中第一个元素概率 1 / length，未取中第一个元素概率（length - 1）/ length
遍历到第二个元素的前提是未取中第一个元素，已经有了（length - 1）/ length这个前提
所以遍历时第二个元素取中概率需要是 1 /（length - 1），这样乘以前提（length - 1）/ length才能得到实际概率 1 / length
**/
```

#### [398. 随机数索引](https://leetcode-cn.com/problems/random-pick-index/)

```c++
int[] nums = new int[] {1,2,3,3,3};
Solution solution = new Solution(nums);

// pick(3) 应该返回索引 2,3 或者 4。每个索引的返回概率应该相等。
solution.pick(3);

// pick(1) 应该返回 0。因为只有nums[0]等于1。
solution.pick(1);

/***
倒序的水塘抽样，要是遇到这个元素，就首先反顺序1/1放入候选，然后遇到第二个的时候用1/2的概率替换，第三个1/3的概率……
**/
vector<int> n;
Solution(vector<int> nums) {
    n=nums;
}
int pick(int target) {
    int count=0;
    int shot;
    int index=-1;
    for (int i=0;i<n.size();i++){
        int nn=n[i];
        if (nn==target)
        {
            count++;
            shot=rand()%count;
            if (shot==0)
                index=i;
        }
    }
    return index;
}
```







#### 557. 反转字符串中的单词 III

```
输入: "Let's take LeetCode contest"
输出: "s'teL ekat edoCteeL tsetnoc" 
```

```c++
//采用字符流 将字符自动切分“ ”
string reverseWords(string s) {
        istringstream sin(s);
        string res = "";
        string t;
        sin >> t;
        reverse(t.begin(),t.end());
        res+=t;
        while(sin >> t)
        {
            res+=" ";
            reverse(t.begin(),t.end());
            res+=t;
        }
        return res;
    }
```

#### 434. 字符串中的单词数

```
输入: "Hello, my name is John"
输出: 5
```

```c++
    // int countSegments(string s) {
    //     istringstream is(s);
    //     int count=0;
    //     string tmp;
    //     while (is>>tmp){
    //         count++;
    //     }
    //     return count;
    // }
    int countSegments(string s){
        int count=0;
        int i=0;
        int len=s.size();
        while (i<len){
 while(i<len&&isspace(s[i]))i++;
            if (i==len) break;
                count++;
            while (i<len&&!isspace(s[i]))i++;
        }
        return count;
    }
```





#### [278. 第一个错误的版本](https://leetcode-cn.com/problems/first-bad-version/)

```
给定 n = 5，并且 version = 4 是第一个错误的版本。

调用 isBadVersion(3) -> false
调用 isBadVersion(5) -> true
调用 isBadVersion(4) -> true

所以，4 是第一个错误的版本。 

    int firstBadVersion(int n) {
        ulong low=0,mid,high=n;
        while (low<high){
            mid=(low+high)/2;
            if (isBadVersion(mid)==true)
                high=mid;
            else low=mid+1;
        }
        return low;
    }
```



#### [233. 数字1的个数](https://leetcode-cn.com/problems/number-of-digit-one/)

给定一个整数 n，计算所有小于等于 n 的非负整数中数字 1 出现的个数。

**示例:**

```
输入: 13
输出: 6 
解释: 数字 1 出现在以下数字中: 1, 10, 11, 12, 13 。
```

```c++
int countDigitOne(int n) {
        // string s;
        // for (int i=1;i<=n;i++){
        //     s+=to_string(i);
        // }
        // int cnt=0;
        // for (auto ss:s){
        //     if (ss=='1')
        //         cnt++;
        // }
        // return cnt;
        unsigned long long num=n,i=1,s=0;
        if(num==-1)
            return 0;

        while(num)              //分别计算个、十、百......千位上1出现的次数，再求和。
        { 
            if(num%10==0)
                s=s+(num/10)*i;

            if(num%10==1) 
                s=s+(num/10)*i+(n%i)+1;

            if(num%10>1) 
                s=s+ceil(num/10.0)*i;

            num=num/10;
            i=i*10;
        }    
        return s;
    }
```









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



#### 最大数

给定一组非负整数，重新排列它们的顺序使之组成一个最大的整数。

**示例 1:**

```
输入: [10,2]
输出: 210
```

**示例 2:**

```
输入: [3,30,34,5,9]
输出: 9534330
```

**说明:** 输出结果可能非常大，所以你需要返回一个字符串而不是整数。

**需要将问题简化为一个字符串比较的逻辑问题，需要注意例如“30”和“3”的顺序 由`330`或者`303`决定。**





#### [598. 范围求和 II](https://leetcode-cn.com/problems/range-addition-ii/)

给定一个初始元素全部为 **0**，大小为 m*n 的矩阵 **M** 以及在 **M** 上的一系列更新操作。

操作用二维数组表示，其中的每个操作用一个含有两个**正整数 a** 和 **b** 的数组表示，含义是将所有符合 **0 <= i < a** 以及 **0 <= j < b** 的元素 **M[i][j]** 的值都**增加 1**。

在执行给定的一系列操作后，你需要返回矩阵中含有最大整数的元素个数。

**示例 1:**

```c++
输入: 
m = 3, n = 3
operations = [[2,2],[3,3]]
输出: 4
解释: 
初始状态, M = 
[[0, 0, 0],
 [0, 0, 0],
 [0, 0, 0]]
执行完操作 [2,2] 后, M = 
[[1, 1, 0],
 [1, 1, 0],
 [0, 0, 0]]
执行完操作 [3,3] 后, M = 
[[2, 2, 1],
 [2, 2, 1],
 [1, 1, 1]]
M 中最大的整数是 2, 而且 M 中有4个值为2的元素。因此返回 4。
// 脑筋急转弯 ，覆盖区域一定是全部重叠区域的值最大，只需要求所有区域的交集
    int maxCount(int m, int n, vector<vector<int>>& ops) {
        int minx = m, miny = n;
        for ( auto val : ops ) {
            minx = ( minx > val[0] ) ? val[0] : minx;
            miny = ( miny > val[1] ) ? val[1] : miny;
        }
        return minx * miny;
    }
```

#### [476. 数字的补数](https://leetcode-cn.com/problems/number-complement/)

给定一个正整数，输出它的补数。补数是对该数的二进制表示取反。

**注意:**

1. 给定的整数保证在32位带符号整数的范围内。
2. 你可以假定二进制数不包含前导零位。

**示例 1:**

```c++
输入: 5
输出: 2
解释: 5的二进制表示为101（没有前导零位），其补数为010。所以你需要输出2。
// 1\直觉就是创建一个掩码，然后和num按位取反之后的结果做与and&
// 2\直觉与掩码做异或 return mask^num
int findComplement(int num) {
        int count = 0;
        int temp = num;
        int mask = 0;
        if (num <= 0)
            return 0;
        while (temp) {
            temp >>= 1;
            // mask |= 1 << (count++);
            mask = (mask<<1) +1;
        }
        // return mask & (~num);
        return mask ^ num;
    }
```







#### [202. 快乐数](https://leetcode-cn.com/problems/happy-number/)

编写一个算法来判断一个数是不是“快乐数”。

一个“快乐数”定义为：对于一个正整数，每一次将该数替换为它每个位置上的数字的平方和，然后重复这个过程直到这个数变为 1，也可能是无限循环但始终变不到 1。如果可以变为 1，那么这个数就是快乐数。

**示例:** 

```c++
输入: 19
输出: true
解释: 
12 + 92 = 82
82 + 22 = 68
62 + 82 = 100
12 + 02 + 02 = 1
// 主要思想，带记忆的递归，如果全局map中出现之前的值，说明出现了循环。
    map <int ,bool> m;
    bool isHappy(int n) {
        int sum=0;
        if (n==1)
            return true;
        if (m[n]==1){return false;}
        m[n]=1;
        while (n){
            sum+=pow(n%10,2);
            n/=10;
        }
        
        return isHappy(sum);
    }
```

#### [917. 仅仅反转字母](https://leetcode-cn.com/problems/reverse-only-letters/)

给定一个字符串 `S`，返回 “反转后的” 字符串，其中不是字母的字符都保留在原地，而所有字母的位置发生反转。

 



**示例 1：**

```
输入："ab-cd"
输出："dc-ba"
```

**示例 2：**

```
输入："a-bC-dEf-ghIj"
输出："j-Ih-gfE-dCba"
```

**示例 3：**

```c++
输入："Test1ng-Leet=code-Q!"
输出："Qedo1ct-eeLg=ntse-T!"

    string reverseOnlyLetters(string S) {
        int l=0,r=S.size()-1;
        char temp;
        while (l<r){
            while(l<S.size()-1&&!isalpha(S[l]))l++;
            while(r>0&&!isalpha(S[r]))r--;
            if (l>r)
                break;
            //
            temp=S[l];
            S[l]=S[r];
            S[r]=temp;
            l++;r--;
        }
        return S;
    }
```













#### [263. 丑数](https://leetcode-cn.com/problems/ugly-number/)

编写一个程序判断给定的数是否为丑数。

丑数就是只包含质因数 `2, 3, 5` 的**正整数**。

**示例 1:**

```c++
输入: 6
输出: true
解释: 6 = 2 × 3
    // 第一思路直接用递归求解
    // 最优解由于2 3 5都是质数，所以直接循环相除。
    bool isUgly(int num) {
        if (num==2||num==3||num==5||num==1)
            return true;
        else if (num==0) return false;
        bool fact=false;
        if (num%2==0){
            fact=fact||isUgly(num/2);
        }
        if (num%3==0){
            fact=fact||isUgly(num/3);
        }
        if (num%5==0){
            fact=fact||isUgly(num/5);
        }
        return fact;
	}
// 循环相除
	if (num == 0) return false;
        while (num % 5 == 0) {
            num /= 5;
        }
        while (num % 3 == 0) {
            num /= 3;
        }
        while (num % 2 == 0) {
            num /= 2;
        }
        return num == 1;

```

你将得到一个字符串数组 `A`。

如果经过任意次数的移动，S == T，那么两个字符串 `S` 和 `T` 是*特殊等价*的。

 

一次*移动*包括选择两个索引 `i` 和 `j`，且 `i％2 == j％2`，并且交换 `S[j]` 和 `S [i]`。

现在规定，*A 中的特殊等价字符串组*是 `A` 的非空子集 `S`，这样不在 `S` 中的任何字符串与 `S` 中的任何字符串都不是特殊等价的。

 

返回 `A` 中特殊等价字符串组的数量。

 



**示例 1：**

```
输入：["a","b","c","a","c","c"]
输出：3
解释：3 组 ["a","a"]，["b"]，["c","c","c"]
```

**示例 2：**

```
输入：["aa","bb","ab","ba"]
输出：4
解释：4 组 ["aa"]，["bb"]，["ab"]，["ba"]
```

**示例 3：**

方法：计数

**思路和算法**

让我们试着表述一个特殊等价的字符串 S*S*，通过找到函数 \mathcal{C}C 使得 S \equiv T \iff \mathcal{C}(S) = \mathcal{C}(T)*S*≡*T*⟺C(*S*)=C(*T*)。

通过交换，我们可以排列偶数索引字母和奇数索引字母。这些排列的特征在于字母的数量：所有这样的排列都有相同的数量，不同的数量会产生不同的排列。

因此，函数 \mathcal{C}(S) =C(*S*)=（S 中偶数索引字母的数量，其后是 S 中奇数索引字母的数量）成功地刻画了这一等价关系。

然后，我们统计出满足 S \in A*S*∈*A* 的 \mathcal{C}(S)C(*S*) 的数量。

```c++
输入：["abc","acb","bac","bca","cab","cba"]
输出：3
解释：3 组 ["abc","cba"]，["acb","bca"]，["bac","cab"]
//我的想法是对奇数的提取出来之后排序，偶数提出排序，然后合并这个作为map的key，最后计数map的key个数即可（也可以使用set）
//最优解，奇偶数分离开来统计计数，然后将整个作为一个字符串放入set中。
class Solution {
    public int numSpecialEquivGroups(String[] A) {
        Set<String> seen = new HashSet();
        for (String S: A) {
            int[] count = new int[52];
            for (int i = 0; i < S.length(); ++i)
                count[S.charAt(i) - 'a' + 26 * (i % 2)]++;
            seen.add(Arrays.toString(count));
        }
        return seen.size();
    }
}
```



#### [43. 字符串相乘](https://leetcode-cn.com/problems/multiply-strings/)

给定两个以字符串形式表示的非负整数 `num1` 和 `num2`，返回 `num1` 和 `num2` 的乘积，它们的乘积也表示为字符串形式。

**示例 1:**

```
输入: num1 = "2", num2 = "3"
输出: "6"
```

**示例 2:**

```c++
输入: num1 = "123", num2 = "456"
输出: "56088"
//直觉想法就是相乘然后进位,只要按照想法来，然后保存结果到乘积里，每次进位的时候避免重复计算即可，也可以用进位的方法做，但是可能比较容易出现差错，还有一点是容易被"123"搞混低位的位置，"123"的字符串低位在左边，而不是逻辑上的低位！！！！！！！
    string multiply(string num1, string num2) {
        if (num1=="0"||num2=="0")
            return "0";
        int len1 = num1.size()-1;
        int len2 = num2.size()-1;
        vector<int> res(len1 + len2 + 2, 0);
        int tempsum;
        for (int i = len1; i >= 0; i--) {

            for (int j = len2; j >= 0; j--) {
                tempsum = (num1[i] - '0')*(num2[j] - '0');
                //先取出低位的内容
                tempsum += res[i + j + 1];
                //高位
                res[i + j] += tempsum / 10;
                //低位
                res[i + j + 1] = tempsum % 10;
            }
        }
        string r;
        for (auto i : res) {
            r.push_back(i + '0');
        }
        //
        int pos = 0;
        while (r[pos] == '0')pos++;
        return r.substr(pos);
    }
```

**说明：**

1. `num1` 和 `num2` 的长度小于110。
2. `num1` 和 `num2` 只包含数字 `0-9`。
3. `num1` 和 `num2` 均不以零开头，除非是数字 0 本身。
4. **不能使用任何标准库的大数类型（比如 BigInteger）**或**直接将输入转换为整数来处理**。





#### [66. 加一](https://leetcode-cn.com/problems/plus-one/)

给定一个由**整数**组成的**非空**数组所表示的非负整数，在该数的基础上加一。

最高位数字存放在数组的首位， 数组中每个元素只存储一个数字。

你可以假设除了整数 0 之外，这个整数不会以零开头。

**示例 1:**

```
输入: [1,2,3]
输出: [1,2,4]
解释: 输入数组表示数字 123。
```

**示例 2:**

```c++
输入: [4,3,2,1]
输出: [4,3,2,2]
解释: 输入数组表示数字 4321。
// 马虎的人注意进位
	vector<int> plusOne(vector<int>& digits) {
        bool carry=true;
        for (int i=digits.size()-1;i>=0;--i){
            digits[i]+=carry;
            if (digits[i]>9){
                carry=true;
                digits[i]%=10;
            }
            else carry=false;
        }
        if (carry)
        {
            vector<int> r(digits.size()+1,1);
            for (int i=0;i<digits.size();i++)r[i+1]=digits[i];
            return r;
        }
        return digits;
    }
```

#### [989. 数组形式的整数加法](https://leetcode-cn.com/problems/add-to-array-form-of-integer/)

对于非负整数 `X` 而言，*X* 的*数组形式*是每位数字按从左到右的顺序形成的数组。例如，如果 `X = 1231`，那么其数组形式为 `[1,2,3,1]`。

给定非负整数 `X` 的数组形式 `A`，返回整数 `X+K` 的数组形式。

 **示例 1：**

```
输入：A = [1,2,0,0], K = 34
输出：[1,2,3,4]
解释：1200 + 34 = 1234
```

**解释 2：**

```
输入：A = [2,7,4], K = 181
输出：[4,5,5]
解释：274 + 181 = 455
```

**示例 3：**

```
输入：A = [2,1,5], K = 806
输出：[1,0,2,1]
解释：215 + 806 = 1021
```

**示例 4：**

```c++
输入：A = [9,9,9,9,9,9,9,9,9,9], K = 1
输出：[1,0,0,0,0,0,0,0,0,0,0]
解释：9999999999 + 1 = 10000000000
//将K转换成vector处理，我倾向于将vector reverse之后相加，不容易出现边界问题
        string addStrings(string num1, string num2) {
        reverse(num1.begin(),num1.end());
        reverse(num2.begin(),num2.end());
        int a,b,carry=0,sum=0;
        string res;
        for (int i=0;i<num1.size()||i<num2.size();i++){
            a=(i<num1.size())?num1[i]-'0':0;
            b=(i<num2.size())?num2[i]-'0':0;
            sum=carry+a+b;
            carry=sum/10;
            res+='0'+sum%10;
        }
        if (carry)
            res+='1';
        reverse(res.begin(),res.end());
        return res;
    }
    
```

 

**提示：**

1. `1 <= A.length <= 10000`
2. `0 <= A[i] <= 9`
3. `0 <= K <= 10000`
4. 如果 `A.length > 1`，那么 `A[0] != 0`



#### [856. 括号的分数](https://leetcode-cn.com/problems/score-of-parentheses/)

给定一个平衡括号字符串 `S`，按下述规则计算该字符串的分数：

- `()` 得 1 分。
- `AB` 得 `A + B` 分，其中 A 和 B 是平衡括号字符串。
- `(A)` 得 `2 * A` 分，其中 A 是平衡括号字符串。

 

**示例 1：**

```
输入： "()"
输出： 1
```

**示例 2：**

```
输入： "(())"
输出： 2
```

**示例 3：**

```
输入： "()()"
输出： 2
```

**示例 4：**

```c++
输入： "(()(()))"
输出： 6
// 可以stack的方式计算；push的时候同时计数，匹配的时候就求和，（可以直接将数字push进去） stack<string>
// 优化方式，直接使用dfs的方式，通过递归求解，提取一个之后递归调用余下的值计算。
// 优化方式，由于不用匹配字符串内容所以可以通过直接对左右括号进行计数得到（ ）左加右减。
```







### 二叉树

#### [94. 二叉树的中序遍历](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

给定一个二叉树，返回它的*中序* 遍历。

**示例:**

```c++
输入: [1,null,2,3]
   1
    \
     2
    /
   3

输出: [1,3,2]
//递归方法
vector<int> res;
vector<int> inorderTraversal(TreeNode* root) {
    if (!root)
        return res;
    inorderTraversal(root->left);
    res.push_back(root->val);
    inorderTraversal(root->right);
    return res;
}

//非递归方法
```







#### [204. 计数质数](https://leetcode-cn.com/problems/count-primes/)

统计所有小于非负整数 *n* 的质数的数量。

**示例:**

```c++
输入: 10
输出: 4
解释: 小于 10 的质数一共有 4 个, 它们是 2, 3, 5, 7 。
/* 第一直觉是，从0-n 挨个判断质数，结果TLE！
   厄拉多筛法采用类似动态规划的方式，创建对应空间，然后从中筛去非质数。！！！
厄拉多塞筛法. 比如说求20以内质数的个数,首先0,1不是质数.2是第一个质数,然后把20以内所有2的倍数划去.2后面紧跟的数即为下一个质数3,然后把3所有的倍数划去.3后面紧跟的数即为下一个质数5,再把5所有的倍数划去.以此类推.
*/
代码的实现上用了非常好的技巧:

def countPrimes(self, n: int) -> int:
        if n < 3:
            return 0     
        else:
            # 首先生成了一个全部为1的列表
            output = [1] * n
            # 因为0和1不是质数,所以列表的前两个位置赋值为0
            output[0],output[1] = 0,0
             # 此时从index = 2开始遍历,output[2]==1,即表明第一个质数为2,然后将2的倍数对应的索引
             # 全部赋值为0. 此时output[3] == 1,即表明下一个质数为3,同样划去3的倍数.以此类推.
            for i in range(2,int(n**0.5)+1): 
                if output[i] == 1:
                    output[i*i:n:i] = [0] * len(output[i*i:n:i])
         # 最后output中的数字1表明该位置上的索引数为质数,然后求和即可.
        return sum(output)
在上面遍历索引的时候用到了一个非常好的技巧. 即i是从(2,int(n**0.5)+1)而非(2,n).这个技巧是可以验证的,比如说求9以内的质数个数,那么只要划掉sqrt(9)以内的质数倍数,剩下的即全为质数. 所以在划去倍数的时候也是从i*i开始划掉,而不是i+i.
     int countPrimes(int n) {
        if (n<1)
            return 0;
        int *p = new int [n+1]{0};
        int count=0;
        p[0]=p[1]=1;
        for (int i=2;i<sqrt(n);i++){
            if (p[i]==0){
                for (int j=i*i;j<n;j+=i){
                    p[j]=1;
                }
            }
        }
        for (int i=2;i<n;i++){
            if (!p[i]) count++;
        }
        return count;
    }
```









#### [101. 对称二叉树](https://leetcode-cn.com/problems/symmetric-tree/)

给定一个二叉树，检查它是否是镜像对称的。

例如，二叉树 `[1,2,2,3,4,4,3]` 是对称的。

```c++
    1
   / \
  2   2
 / \ / \
3  4 4  3

//第一想法是层次遍历然后做回文比较
//然后想可不可以做前序遍历和反向(先右后左)前序遍历
//* 最优解  
//使用递归方法，左右分叉，同向查找比对，不符合就false
//使用迭代方法，创建队列，但是不按层次遍历顺序，而是按照反向顺序插入。

class Solution {
public:
    bool isSymmetric(TreeNode* root) {
        return ismirror(root,root);
    }
    
    bool ismirror(TreeNode* p,TreeNode* q){
        if(!p&&!q)//都为NULL
            return true;
        if(!p||!q)//有一个为NULL
            return false;
        if(p->val==q->val)
            return ismirror(p->left,q->right)&&ismirror(p->right,q->left);
        return false;
    }
};

//迭代方法，操作分离左右按照顺序入站
bool go(TreeNode *root){
        queue<TreeNode *> q;
        q.push(root);
        q.push(root);
        while(!q.empty()){
            TreeNode *c1=q.front();
            q.pop();
            TreeNode *c2=q.front();
            q.pop();
            if (!c1&&!c2)
                continue;
            else if (!c1||!c2)
                return false;
            else if (c1->val==c2->val){
                q.push(c1->left);
                q.push(c2->right);
                
                q.push(c1->right);
                q.push(c2->left);
            }
            else return false;
        }
        return true;
    }
```



#### [606. 根据二叉树创建字符串](https://leetcode-cn.com/problems/construct-string-from-binary-tree/)



你需要采用前序遍历的方式，将一个二叉树转换成一个由括号和整数组成的字符串。

空节点则用一对空括号 "()" 表示。而且你需要省略所有不影响字符串与原始二叉树之间的一对一映射关系的空括号对。

**示例 1:**

```c++
输入: 二叉树: [1,2,3,4]
       1
     /   \
    2     3
   /    
  4     

输出: "1(2(4))(3)"

解释: 原本将是“1(2(4)())(3())”，
在你省略所有不必要的空括号对之后，
它将是“1(2(4))(3)”。
输入: 二叉树: [1,2,3,null,4]
       1
     /   \
    2     3
     \  
      4 

输出: "1(2()(4))(3)"

//第一直觉是使用递归
//递归需要考虑很多条件因素，一开始使用void作为返回值，结果在调用的时候遇到各种bug，然后改成string通过
    string tree2str(TreeNode* t) {
        string res = "";
        if(t==NULL) return res;
        if(t->left==NULL && t->right == NULL){
            res.append(to_string(t->val));
            return res;
        }
        res.append(to_string(t->val));
        res+=("("+tree2str(t->left)+")");
        if(t->right!=NULL)
        res+=("("+tree2str(t->right)+")");
        return res;
    }

```



#### [116. 填充每个节点的下一个右侧节点指针](https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node/)

给定一个**完美二叉树**，其所有叶子节点都在同一层，每个父节点都有两个子节点。二叉树定义如下：

```c++
struct Node {
  int val;
  Node *left;
  Node *right;
  Node *next;
}
//拿到的时候毫无头绪，想不到递归的条件，想着两个节点一起进入递归，然后seal在一起，但这样就无法处理相邻的节点。看了评论恍然大悟。。。还是想法不够清晰。
//递归版

class Solution {
public:
    void connect(TreeLinkNode *root) {
        if (root == NULL || root->left == NULL)
            return;
        root->left->next = root->right;
        if (root->next)
            root->right->next = root->next->left;
        connect(root->left);
        connect(root->right);
    }
};
//非递归就是用两个指针，分别指两层。
	void connect(TreeLinkNode *root) {
        if (root == NULL)
            return;
        TreeLinkNode* pre = root;
        TreeLinkNode* cur = NULL;
        while (pre->left)
        {
            cur = pre;
            while (cur)
            {
                cur->left->next = cur->right;
                if (cur->next)
                    cur->right->next = cur->next->left;
                cur = cur->next;
            }
            pre = pre->left;
        }
    }
```

填充它的每个 next 指针，让这个指针指向其下一个右侧节点。如果找不到下一个右侧节点，则将 next 指针设置为 `NULL`。

初始状态下，所有 next 指针都被设置为 `NULL`。

 

**示例：**

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2019/02/15/116_sample.png)























































#### [279. 完全平方数](https://leetcode-cn.com/problems/perfect-squares/)

给定正整数 *n*，找到若干个完全平方数（比如 `1, 4, 9, 16, ...`）使得它们的和等于 *n*。你需要让组成和的完全平方数的个数最少。

**示例 1:**

```c
输入: n = 12
输出: 3 
解释: 12 = 4 + 4 + 4.
    
```









### 创新思维

#### [521. 最长特殊序列 Ⅰ](https://leetcode-cn.com/problems/longest-uncommon-subsequence-i/)

给定两个字符串，你需要从这两个字符串中找出最长的特殊序列。最长特殊序列定义如下：该序列为某字符串独有的最长子序列（即不能是其他字符串的子序列）。

**子序列**可以通过删去字符串中的某些字符实现，但不能改变剩余字符的相对顺序。空序列为所有字符串的子序列，任何字符串为其自身的子序列。

输入为两个字符串，输出最长特殊序列的长度。如果不存在，则返回 -1。

**示例 :**

```c++
输入: "aba", "cdc"
输出: 3
解析: 最长特殊序列可为 "aba" (或 "cdc")
    // 第一遍没有读懂，**空序列为所有字符串的子序列，任何字符串为其自身的子序列。**关键在于这句
    // 返回最长的
    int findLUSlength(string a, string b) {
        if(a==b)
            return -1;
        else {
            return a.size()>=b.size()?a.size():b.size();
        }
    }
```







#### [836. 矩形重叠](https://leetcode-cn.com/problems/rectangle-overlap/)

矩形以列表 `[x1, y1, x2, y2]` 的形式表示，其中 `(x1, y1)` 为左下角的坐标，`(x2, y2)` 是右上角的坐标。

如果相交的面积为正，则称两矩形重叠。需要明确的是，只在角或边接触的两个矩形不构成重叠。

给出两个矩形，判断它们是否重叠并返回结果。

**示例 1：**

```c++
输入：rec1 = [0,0,2,2], rec2 = [1,1,3,3]
输出：true

[7,8,13,15]
[10,8,12,20]
// 注意一点！包含两种重叠方式，第一种是边界点在对方的矩阵里，第二种是边界点不在但是边线相交
// 两种思路，直觉判断相交的可能第一种是按照deltaX'>0 *deltaX''<0 用乘积 ***注意乘积可能会超限
		x1,y1,x2,y2=rec1
        x3,y3,x4,y4=rec2
        return (x3-x2)*(x4-x1)<0 and (y3-y2)*(y4-y1)<0
    //第二种思路是 ，通过判断不相交的情况，然后取反
    return !(rec1[0]>=rec2[2] || rec1[2]<=rec2[0] || rec1[1]>=rec2[3] || rec1[3]<=rec2[1]);
bool isRectangleOverlap(vector<int>& rec1, vector<int>& rec2) {
        return !(rec1[0]>=rec2[2] || rec1[2]<=rec2[0] || rec1[1]>=rec2[3] || rec1[3]<=rec2[1]);
    }
```



#### [264. 丑数 II](https://leetcode-cn.com/problems/ugly-number-ii/)

编写一个程序，找出第 `n` 个丑数。

丑数就是只包含质因数 `2, 3, 5` 的**正整数**。

**示例:**

```c++
输入: n = 10
输出: 12
解释: 1, 2, 3, 4, 5, 6, 8, 9, 10, 12 是前 10 个丑数。
1 是丑数。
n 不超过1690。
// 直觉穷举，n=1690时乘积会很大，穷举会超时 达到：2123366400
// 三指针方法，一个生成队列，然后三个指针指向的对象分别乘2，3，5，然后比较大小。原理是因为每次都从生成的序列中取最小的那个加到生成队列最后（需要注意去重，但是由于三个质因子，所以不会重复）
//2    a 
// 1 2 3 4 5
//3  b
//5  c
// 比较可以得出下一个值是2*v[b]||2*v[a]
    int nthUglyNumber(int n) {
        vector <int> v;
        v.push_back(0);
        v.push_back(1);
        int a,b,c;
        a=b=c=1;
        for (int i=1;i<n;i++){
            int ra=v[a]*2,rb=v[b]*3,rc=v[c]*5;
            int minimal= min(ra,min(rb,rc));
            // 需要注意，当
            if (minimal==ra)a++;
            if (minimal==rb)b++;
            if (minimal==rc)c++;
            v.push_back(minimal);
        }
        return v[n];
    }

```



#### [401. 二进制手表](https://leetcode-cn.com/problems/binary-watch/)

二进制手表顶部有 4 个 LED 代表**小时（0-11）**，底部的 6 个 LED 代表**分钟（0-59）**。

每个 LED 代表一个 0 或 1，最低位在右侧。

![img](https://upload.wikimedia.org/wikipedia/commons/8/8b/Binary_clock_samui_moon.jpg)

例如，上面的二进制手表读取 “3:25”。

给定一个非负整数 *n* 代表当前 LED 亮着的数量，返回所有可能的时间。

**案例:**

```c++
输入: n = 1
返回: ["1:00", "2:00", "4:00", "8:00", "0:01", "0:02", "0:04", "0:08", "0:16", "0:32"]
//拿到之后毫无头绪，想着应该是一个回溯的问题，但是可能会出现分钟数超过60的情况要在回溯中删除
// 评论中的一个创新方法，直接遍历所有的情况，从中筛选出二进制位数符合的方式，是一种反向思维的方式，可以借鉴，大规模的情况下还是需要使用回溯
/*回溯步骤
第一步：我们对于给定的亮的灯个数num进行分解，分成小时灯个数hoursCnt、分钟灯个数minsCnt。
第二步：然后对小时灯个数hoursCnt小时的情况进行构造。（比如，当hoursCnt == 1，可以构造成小时为 1，2，4，8）
第三步：接着对分钟灯个数minsCnt分钟的情况进行构造。（比如，当minsCnt== 1，可以构造成分钟为 1，2，4，8，16，32）
第四步：最后将小时的集合和分钟集合进行搭配组合。
*/
vector<string> readBinaryWatch(int num) {
        vector<string> res;
        for (int i=0;i<12;i++){
            for (int j=0;j<60;j++){
                if (count1(i)+count1(j)==num)
                {
                    if (j<10)
                        res.push_back(to_string(i)+":0"+to_string(j));
                    else res.push_back(to_string(i)+":"+to_string(j));
                }
                    
            }
        }
        return res;
    }
    int count1(int a){
        int c=0;
        while (a){
            if (a&1)c++;
            a>>=1;
        }
        return c;
    }
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
//注意必须满足严格比较方式，即cmp(a,a)==false;!!!!!!!!!!!
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

### 初始化列表

```c++
    int a1, a2;
    vector <int> res = { a1,a2 };
	int a[20]={0}//不允许除了0以外的初始化 使用memset(a,0,100)//按字节填充
// 可以直接返回初始化列表
vector<int> function(int _x,int _y){
    return {_x,_y};
}	
```
### 最大整数/最小整数

```c++
INT_MAX;
INT_MIN;
// defined by MSVC***********
```

### 运行效率

![1554338466126](/C:/Users/zch/AppData/Roaming/Typora/typora-user-images/1554338466126.png)

不考虑优化的情况下采用G++编译结果，结果和期望一致，大循环在外的情况下会有更多的int创建和消除，最终效率低一点。





## algorithm库

头文件：`#include <algorithm>`

时间复杂度：一次查询O(log n)，n为数组长度。

![img](/../assets/20180605171241611)

### **lower_bound**

功能：查找非递减序列[first,last) 内第一个大于或等于某个元素的位置。

返回值：如果找到返回找到元素的地址否则返回last的地址。（这样不注意的话会越界，小心）

用法：`int t=lower_bound(a+l,a+r,key)-a；`(a是数组)。

### **upper_bound**

功能：查找非递减序列[first,last) 内第一个大于某个元素的位置。

返回值：如果找到返回找到元素的地址否则返回last的地址。（同样这样不注意的话会越界，小心）

用法：`int t=upper_bound(a+l,a+r,key)-a；`(a是数组)。

### sort

```c++
sort(a.begin(),a.end(),[](int a,int b){return a>b;}) ;
//equal to greater<int> *ref <functional>
```



### reverse

```c++
sort(a.begin(),a.end()) //reverse the array;
```



### isspace





### isalpha



### isalnum

