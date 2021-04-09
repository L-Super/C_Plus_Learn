# C++学习笔记

## 指针

对指针可以执行加减运算
指针加1是加上一个基类型的长度
指针减1是减掉一个基类型的长度
如：

```c++
int a,*pi=&a;
double b,*pd=&b;
++pi;	//pi的值增加4
++pd;	//pd的值增加8
```

```c++
int main()
{
	using namespace std;
	short dates[4]{1,2,3,4}, * pti, index;
	double bills[4]{}, * ptf;
	
	cout<<"          \t\t  short  \t \t double \n";
	for (index = 0; index < 4; index++)
		cout << " array:  index:"<< index << " dates + index: " <<int(dates + index) << " bills + index: "<<
		int(bills + index) << endl;
	pti = dates;
	ptf = bills;
	cout << "               short \t double\n";
		
	for (index = 0; index < 4; index++)
		cout<<"pointers+"<< index << "   " << int(pti + index) <<"\t"<<
			int(ptf + index) << endl;
	return 0;
}
```

![image-20210326190938690](C++学习笔记.assets/image-20210326190938690.png)

即，short型数组加上1个基型长度2，double型数组为加8个基型长度，**dates + index**与**pti + index**等效，即由dates[0]变为dates[1]

## 内存模型和名称空间

### 预处理

**头文件管理**

在同一个文件中只能将同一个头文件包含一次。

基于预处理器编译指令**#ifndef(if not defined)**，可以避免多次包含同一个头文件。

如果在同一个文件中遇到其他包含coordin.h的代码，编译器就知道COORDIN_H_已经被定义了，从而跳到#endif之后。没有定义的时候，才处理**#ifndef 和 #endif**之间的语句。

```c++
#ifndef COORDIN_H_
#define COORDIN_H_
#endif
```

预处理变量有两种状态：已定义和未定义。

#define指令把一个名字设定为预处理变量，另外两个指令则分别检查某个指定的预处理变量是否已经定义：

**#ifdef**当且仅当变量已定义时为真，

**#ifndef**当且仅当变量未定义时为真。

一旦检查结果为真，则执行后续操作直至遇到#endif指令为止。

第一次包含coordin.h时，#ifndef的检查结果为真，预处理器将顺序执行后面的操作直至遇到#endif为止。此时，预处理变量SALES_DATA_H的值将变为已定义，而且coordin.h也会被拷贝到我们的程序中来。

后面如果再一次包含coordin.h，则#ifndef的检查结果将为假，编译器将忽略#ifndef到#endif之间的部分。

整个程序中的预处理变量包括**头文件保护符必须唯一**，通常的做法是基于头文件中类的名字来构建保护符的名字，以确保其唯一性。为了避免与程序中的其他实体发生名字冲突，**一般把预处理变量的名字全部大写**。

## 引用

### 引用 vs 指针

引用很容易与指针混淆，它们之间有三个主要的不同：

- 不存在空引用。引用必须连接到一块合法的内存。
- 一旦引用被初始化为一个对象，就不能被指向到另一个对象。指针可以在任何时候指向到另一个对象。
- 引用必须在创建时被初始化。指针可以在任何时间被初始化。

总结：

1. 定义引用时一定要将其初始化成引用某个变量。
2. 初始化后，它就一直引用该变量，不会再引用别
   的变量了。
3. 引用只能引用变量，不能引用常量和表达式。

### 引用做函数参数

**作用：**函数传参时，可以利用引用的技术让形参修饰实参

**优点：**可以简化指针修改实参

**示例：**

```C++
//1. 值传递
void mySwap01(int a, int b) {
	int temp = a;
	a = b;
	b = temp;
}

//2. 地址传递
void mySwap02(int* a, int* b) {
	int temp = *a;
	*a = *b;
	*b = temp;
}

//3. 引用传递
void mySwap03(int& a, int& b) {
	int temp = a;
	a = b;
	b = temp;
}

int main() {

	int a = 10;
	int b = 20;

	mySwap01(a, b);
	cout << "a:" << a << " b:" << b << endl;

	mySwap02(&a, &b);
	cout << "a:" << a << " b:" << b << endl;

	mySwap03(a, b);
	cout << "a:" << a << " b:" << b << endl;

	return 0;
}

```

> 总结：通过引用参数产生的效果同按地址传递是一样的。引用的语法更清楚简单

### 引用做函数返回值

作用：引用是可以作为函数的返回值存在的

注意：**不要返回局部变量引用**

用法：函数调用作为左值

**示例：**

```C++
//返回局部变量引用
int& test01() {
	int a = 10; //局部变量
	return a;
}

//返回静态变量引用
int& test02() {
	static int a = 20;
	return a;
}

int main() {

	//不能返回局部变量的引用
	int& ref = test01();
	cout << "ref = " << ref << endl;
	cout << "ref = " << ref << endl;

	//如果函数做左值，那么必须返回引用
	int& ref2 = test02();
	cout << "ref2 = " << ref2 << endl;
	cout << "ref2 = " << ref2 << endl;

	test02() = 1000;

	cout << "ref2 = " << ref2 << endl;
	cout << "ref2 = " << ref2 << endl;

	return 0;
}
```

### 引用的本质

本质：**引用的本质在c++内部实现是一个指针常量.**

讲解示例：

```C++
//发现是引用，转换为 int* const ref = &a;
void func(int& ref){
	ref = 100; // ref是引用，转换为*ref = 100
}

int main(){
	int a = 10;
    //自动转换为 int* const ref = &a; 指针常量是指针指向不可改，也说明为什么引用不可更改
	int& ref = a; 
	ref = 20; //内部发现ref是引用，自动帮我们转换为: *ref = 20;
    
	cout << "a:" << a << endl;
	cout << "ref:" << ref << endl;
    
	func(a);
	return 0;
}
```

结论：C++推荐用引用技术，因为语法方便，引用本质是指针常量，但是所有的指针操作编译器都帮我们做了

## 类和对象

![img](https://www.runoob.com/wp-content/uploads/2015/05/cpp-classes-objects-2020-12-10-11.png)

```c++
class className{
    //说明类成员的可访问范围
private:
//私有属性和函数
public:
//公有属性和函数
protected:
//保护属性和函数
};
```

如果某个成员没有上述关键字,则缺省地被认为是私有成员。

```c++
class Man{
int nAge;//私有成员

public:
void SetName(char szName);
};
```

### 构造函数

构造函数的名称与类的名称是完全相同的，并且不会返回任何类型，也不会返回 void。构造函数可用于为某些成员变量设置初始值。

++++

1. 名字与类名相同,可以有参数,不能有返回值(void也不行)

2. 作用是对对象进行初始化,如给成员变量赋初值

3. 如果定义类时没写构造函数,则编译器生成一个默认的无参数
   的构造函数
   + 默认构造函数无参数,不做任何操作

#### 构造函数的分类及调用

两种分类方式：

​	按参数分为： 有参构造和无参构造

​	按类型分为： 普通构造和拷贝构造

三种调用方式：

+ 括号法

+ 显示法
+ 隐式转换法

```C++
//1、构造函数分类
// 按照参数分类分为 有参和无参构造   无参又称为默认构造函数
// 按照类型分类分为 普通构造和拷贝构造

class Person {
public:
	//无参（默认）构造函数
	Person() {
		cout << "无参构造函数!" << endl;
	}
	//有参构造函数
	Person(int a) {
		age = a;
		cout << "有参构造函数!" << endl;
	}
	//拷贝构造函数
	Person(const Person& p) {
		age = p.age;
		cout << "拷贝构造函数!" << endl;
	}
	//析构函数
	~Person() {
		cout << "析构函数!" << endl;
	}
public:
	int age;
};

//2、构造函数的调用
//调用无参构造函数
void test01() {
	Person p; //调用无参构造函数
}

//调用有参的构造函数
void test02() {

	//2.1  括号法，常用
	Person p1(10);
	//注意1：调用无参构造函数不能加括号，如果加了编译器认为这是一个函数声明
	//Person p2();

	//2.2 显式法
	Person p2 = Person(10); 
	Person p3 = Person(p2);
	//Person(10)单独写就是匿名对象  当前行结束之后，马上析构

	//2.3 隐式转换法
	Person p4 = 10; // Person p4 = Person(10); 
	Person p5 = p4; // Person p5 = Person(p4); 

	//注意2：不能利用 拷贝构造函数 初始化匿名对象 编译器认为是对象声明
	//Person p5(p4);
}

int main() {
	test01();
	//test02();

	return 0;
}
```



#### 拷贝构造函数



### 析构函数

类的**析构函数**是类的一种特殊的成员函数，它会在每次删除所创建的对象时执行。

析构函数的名称与类的名称是完全相同的，只是在前面加了个波浪号（~）作为前缀，它不会返回任何值，也不能带有任何参数。析构函数有助于在跳出程序（比如关闭文件、释放内存等）前释放资源。



## 模板 (template)

泛型编程主要就是利用模板

模板机制有函数模板和类模板

+ template \<typename type>
+ template \<class type>

```c++
//函数模板
//声明模板，告诉T不报错，是一个通用数据类型
template<typename T>
void Swap_template(T &a,T &b)
{
    ...
}
```

两种方式使用函数模板

```c++
//1.自动类型推导
	Swap_template(a, b);

//2.显示指定类型
	Swap_template<int>(a, b);
```

### 类模板

1. 类模板没有自动类型推导使用方式
2. 类模板在模板参数列表中可以有默认参数

使用类模板时，如果在模板外写实现时，模板的名字要加上类型

```c++
template<class NameType,class AgeType>
class Person
{
public:
	Person(NameType name, AgeType age);
	NameType m_name;
	AgeType m_age;
	void showPerson();
};
//需要如下写，才能通过编译
template<class NameType, class AgeType>
Person<NameType, AgeType>::Person(NameType name,AgeType age)
{
	this->m_name = name;
	this->m_age = age;
	
}
template<class NameType, class AgeType>
void Person<NameType, AgeType>::showPerson()
{
	cout << "name: " << this->m_name 
        << "age: " << this->m_age << endl;
}
```

模板的实现和声明，尽量放在一个文件里，分开麻烦，这个和类定义不一样。

类模板在模板参数列表中可以有默认参数:

```c++
template<class NameType,class AgeType=int>//加上int
class Person
{
public:
	Person(NameType name, AgeType age);
}
void test3()
{
	Person<string> p1("孙悟空", 999);
    //template<class NameType,class AgeType=int>加上int后，即可不显示指定int类型
    //只有类模板才可这样使用
}
```

####  类模板对象做函数参数

一共有三种传入方式：

1. 指定传入的类型   --- 直接显示对象的数据类型
2. 参数模板化           --- 将对象中的参数变为模板进行传递
3. 整个类模板化       --- 将这个对象类型 模板化进行传递

**示例：**

```C++
#include <string>
//类模板
template<class NameType, class AgeType = int> 
class Person
{
public:
	Person(NameType name, AgeType age)
	{
		this->mName = name;
		this->mAge = age;
	}
	void showPerson()
	{
		cout << "name: " << this->mName << " age: " << this->mAge << endl;
	}
public:
	NameType mName;
	AgeType mAge;
};

//1、指定传入的类型
void printPerson1(Person<string, int> &p) 
{
	p.showPerson();
}
void test01()
{
	Person <string, int >p("孙悟空", 100);
	printPerson1(p);
}

//2、参数模板化
template <class T1, class T2>
void printPerson2(Person<T1, T2>&p)
{
	p.showPerson();
    //typeid可查看类型
	cout << "T1的类型为： " << typeid(T1).name() << endl;
	cout << "T2的类型为： " << typeid(T2).name() << endl;
}
void test02()
{
	Person <string, int >p("猪八戒", 90);
	printPerson2(p);
}

//3、整个类模板化
template<class T>
void printPerson3(T & p)
{
	cout << "T的类型为： " << typeid(T).name() << endl;
	p.showPerson();

}
void test03()
{
	Person <string, int >p("唐僧", 30);
	printPerson3(p);
}

int main() {
	test01();
	test02();
	test03();
	system("pause");
	return 0;
}
```

总结：

* 通过类模板创建的对象，可以有三种方式向函数中进行传参
* 使用比较广泛是第一种：指定传入的类型

## STL (Standard Template Library)

### STL六大组件简介

容器、算法、迭代器、仿函数、适配器、分配器

![image-20210328220745040](C++学习笔记.assets/image-20210328220745040.png)

+ 容器（Containers）：各种数据结构，如Vector,List,Deque,Set,Map,用来存放数据
+ 算法（Algorithms）：各种常用算法如Sort,Search,Copy,Erase,从实现的角度来看，STL算法是一种Function Templates。
+ 迭代器（Iterators）：用来在一个对象群集（collection of objects）的元素上进行遍历。这个对象群集或许是个容器，或许是容器的一部分。迭代器的主要好处是，为所有容器提供了一组很小的公共接口。
+ 仿函数（Functors）： 行为类似函数，可作为算法的某种策略（Policy）,从实现的角度来看，仿函数是一种重载了Operator()的Class 或 Class Template。一般函数指针可视为狭义的仿函数。
+ 配接器（适配器）（Adapters）：一种用来修饰容器（Containers）或仿函数（Functors）或迭代器（Iterators）接口的东西，为已有的类提供新的接口，目的是简化、约束、使之安全、隐藏或者改变被修改类提供的服务集合。
+ 分配器（Allocators）：负责空间配置与管理，从实现的角度来看，配置器是一个实现了动态空间配置、空间管理、空间释放的Class Template。

![image-20210328225735142](C++学习笔记.assets/image-20210328225735142.png)



### String

标准库类型string表示可变长的字符序列，使用string类型必须首先包含string头文件。

| 初始化String对象的方式 |                                                       |
| ---------------------- | ----------------------------------------------------- |
| String s1              | 默认初始化，s1是空串                                  |
| String s2(s1)          | s2是s1的副本                                          |
| String s2=s1           | 等价于s2(s1),s2是s1的副本                             |
| String s1("abc")       | s1是字面值"value"的副本，除了字面值最后的那个空字符外 |
| String s1="abc"        | 等价于s1("value"),s1是字面值"value"的副本             |
| String s1(n,'a')       | 把s1初始化为由连续n个字符c组成的串                    |

#### 直接初始化和默认初始化

如果使用等号（=)初始化一个变量，实际上执行的是拷贝初始化（copy initialization),编译器把等号右侧的初始值拷贝到新创建的对象中去。

与之相反，如果不使用等号，则执行的是直接初始化

#### String对象操作

|       |                                                              |
| ----- | ------------------------------------------------------------ |
| os<<s | 将s写到输出流OS当中， 返回OS |
| is>>s | 从 is中读取字符串赋给S, 字符串以空白分隔， 返回is            |
| getline(is, s) | 从 is中读取一行赋给S, 返回is                                 |
| s.empty() | s为空返回true, 否则返回false                                 |
| s.size() | 返回s中字符的个数 |
| s[n] |                  返回s中第n个字符的引用， 位置n 从 0计起   |
| sl+s2 | 返回sl和s2连接后的结果      |
| s1=s2 | 用s2的副本代替sl中原来的字符 |
| sl==s2<br />s1!=s2 | 如果sl和s2中所含的字符完全一样，则它们相等； string对象的相等性判断对字母的大小写敏感 |
| <, <=, >, >= | 利用字符在字典中的顺序进行比较， 且对字母的大小写敏感 |

**使用getline读取一整行**

```c++
int main()
{
    string line;
    //每次读入一整行， 直至到达文件末尾
    while (getline (cin, line)) 
        COUt << line << endl; 
    return 0; 
}
```

注：line中不包含换行符,触发getline函数返回的那个换行符实际上被丢弃掉了，得到的string对 象中并不包含该换行符。

**只输出非空的行**

```c++
//每次读入一整行，遇到空行直接跳过 
while (getline(cin, line)) 
    if(!line.empty())
        cout<<line<<endl;
```

**输出其中超过n个字符的行**

```c++
String line; 
／／每次读入一整行，输出其中超过80个字符的行 
while (getline(cin, line)) 
    if(line.size()>80)
        cout<<line<<endl;
```

**string:: size_ type类型**

对千size函数来说，返回一个int或者返回一个unsigned似乎都是合情合理的。但其实size函数返回的是一个**string: :size_type**类型的值。

在具体使用的时候，通过作用域操作符来表明名字sizetype是在类string中定义的。尽管我们不太清楚string::size_type类型的细节，但有一点是肯定的：它是一个无符号类型的值，而且能足够存放下任何string对象的大小。所有用于存放string类的size函数返回值的变量，都应该是**string::size_type**类型的。

在C++11新标准中，允许编译器通过auto或者decltype来推断变量的类型：

`auto len = line.size(); // len的类型是string::size_type `

由千size函数返回的是一个无符号整型数，因此切记，如果在表达式中混用了带符
号数和无符号数将可能产生意想不到的结果。例如，假设n是一个具有负值的int,则表达式s.size()<n的判断结果几乎肯定是true。这是因为负值n会自动地转换成一个比较大的无符号值。

#### 字面值和string对象相加

当把string对象和字符字面值及字符串字面值混在一条语句中使用时，必须确保每个加号运算符**+**两侧至少有一个string对象

```
string s4 = s1 + ","	//正确：把一个string对象和 个字面值相加
string s5 = "hello" + ","; 	//错误： 两个运算对象都不是string

string s6 = s1 + ", " + "world"; //正确：每个加法运算符都有一个运算对象是string
string s7 = "hello" + ", " + s2; //错误：不能把字面值直接相加
```

注：

s6式子可以用如下形式分组：

```
string s6 = (s1 + ", ") + "world";
```

其中子表达式`s1+","`的结果是一个string对象，它同时作为第二个加法运算符的左侧运算对象，因此上述语句和下面的两个语句是等价的：

```
string tmp=s1+"，"；//正确：加法运算符有一个运算对象是string
string s6=tmp+"world"；//正确：加法运算符有一个运算对象是string
```

s7的初始化是非法的，根据其语义加上括号后就成了下面的形式：

```
strings7=（"hello"+"，"）+s2；//错误：不能把字面值直接相加
```

#### 处理 string 对象中的字符

cctype头文件中的函数

| isalnum(c)  | 当c是字母或数字时为真                                        |
| ----------- | ------------------------------------------------------------ |
| isalpha(c)  | 当c是字母时为真                                              |
| iscntrl(c)  | 当c是控制字符时为真                                          |
| isdigit(c)  | 当c是数字时为真                                              |
| isgraph(c)  | 当c不是空格但可打印时为真                                    |
| islower(c)  | 当c是小写字母时为真                                          |
| isprint(c)  | 当c是可打印字符时为真（即c是空格或c具有可视形式）            |
| ispunct(c)  | 当c是标点符号时为真（即c不是控制字符、数字、字母、可打印空白中的一种） |
| isspace(c)  | 当c是空白时为真（即c是空格、横纵向制表符、回车符、换行符、进纸符中的一种） |
| isupper(c)  | 当c是大写字母时为真                                          |
| isxdigit(c) | 当c是十六进制数字时为真                                      |
| tolower(c)  | 如果c是大写字母，输出对应的小写字母：否则原样输出c           |
| toupper(c)  | 如果c是小写字母，输出对应的大写字母：否则原样输出c           |

#### 处理每个字符？使用基于范围的for语句

C++11新标准提供的一种语句：范围for (range for)语句。这种语句遍历给定序列中的每个元素并对序列中的每个值执行某种操作， 其语法形式是：

```c++
for(declaration : expression)
	statement
```


其中，**declaration** 负责定义一个变量， 用于访问序列中的基础元素。每次迭代，**declaration**变量会被初始化为 **expression**的下一个元素值。**expression**是一个对象， 用于表示一个序列。

```c++
string str("some string"); 
//每行检出str中的一个字符，
for (auto c : str) 
COUt<< c << endl; 
```

此例中，通过使用**auto**关键字让编译器来决定变量c的类型，这里c的类型是char。

统计string对象中标点符号的个数

```C++
string s ("Hello World!!!"); 
// punct_cnt的类型和s.size的返回类型一样；
decltype(s.size()) punct_cnt = 0; 
//统计标点符号数量
for (auto c : s) 
    if (ispunct (c))//对于s中的每个字符，如果该字符是标点符号 
        ++punct_cnt;//将标点符号的计数值加
cout << punct_cnt <<" punctuation characters in"<< s << endl;
```

使用decltype关键字，声明计数变量punct_cnt， 它的类型是s.size函数返回值的类型，也就是string::size_type。

**使用范围for语句改变字符串中的字符**

如果想要改变string对象中字符的值，必须把循环变量定义成引用类型

+ 把字符串改写为大写字母

```c++
string s("hello,world!!！");
//转换成大写形式
for (auto &c : s) //对于s中的每个字符（注意:C是引用）
c = toupper(c); //C是一个引用，因此赋值语句将改变s中字符的值
cout << s << endl;
```

上述代码的输出结果将是：HELLO WORLD ! ! ! 

每次迭代时，变量**c**引用**string**对象s的下一个字符，赋值给**c**也就是在改变**s**中对应字符的值。

**想访问string对象中的单个字符**

有两种方式： 一种是使用下标， 另外一种是使用迭代器。
下标运算符（［］）接收的输入参数是string::size_type类型的值， 这个参数表示要访问的字符的位置； 返回值是该位置上字符的引用。

```c++
if (! s. empty()) 
cout << s [O] << endl;//输出 s的笫一个字符
```

### Vector

标准库类型vector表示对象的集合， 其中所有对象的类型都相同。集合中的每个对象都有一个与之对应的索引，索引用于访问对象。

Vector是一个类模板。

#### 定义和初始化Vector对象

| vector<T> v1              | v1是一个空vector, 它潜在的元素是T类型的， 执行默认初始化 |
| ------------------------- | -------------------------------------------------------- |
| vector<T> v2 (v1)         | v2中包含有v1所有元素的副本                               |
| vector<T> v2 = v1         | 等价于v2(v1), v2中包含有v1所有元素的副本                 |
| vector<T> v3(n, val)      | v3包含了n个重复的元素， 每个元素的值都是val              |
| vector<T> v4 (n)          | v4包含了n个重复地执行了值初始化的对象                    |
| vector<T>v5 {a,b,c ... }  | v5包含了初始值个数的元素，每个元素被赋予相应的初始值     |
| vector<T> v5={a,b,c ... } | 等价于vector<T> v5 {a,b,c ... }                          |



