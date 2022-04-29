# C++ 中的sort()排序函数用法

`sort(first_pointer,first_pointer+n,cmp)`

该函数可以给数组，或者链表list、向量排序。

实现原理：sort并不是简单的快速排序，它对普通的快速排序进行了优化，此外，它还结合了插入排序和推排序。系统会根据你的数据形式和数据量自动选择合适的排序方法，这并不是说它每次排序只选择一种方法，它是在一次完整排序中不同的情况选用不同方法，比如给一个数据量较大的数组排序，开始采用快速排序，分段递归，分段之后每一段的数据量达到一个较小值后它就不继续往下递归，而是选择插入排序，如果递归的太深，他会选择推排序。

此函数有3个参数：

参数1：第一个参数是数组的首地址，一般写上数组名就可以，因为数组名是一个指针常量。

参数2：第二个参数相对较好理解，即首地址加上数组的长度n（代表尾地址的下一地址）。

参数3：默认可以不填，如果不填sort会默认按数组升序排序。也就是1,2,3,4排序。也可以自定义一个排序函数，改排序方式为降序什么的，也就是4,3,2,1这样。

使用此函数需先包含：

`\#include <algorithm>`
并且导出命名空间：

`using namespace std;`

简单例子：对数组A的0~n-1元素进行升序排序，只要写sort(A,A+n)即可；对于向量V也一样，sort(v.begin(),v.end())即可。

自己编写排序规则函数

例如：

```c++
bool compare(int a,int b)
{
return a<b; //升序排列，如果改为return a>b，则为降序

}
```

## sort扩展

sort不只是能像上面那样简单的使用，我们可以对sort进行扩展，关键就在于第三个参数<cmp比较函数>，我们想降序排列，或者说我不是一个简简单单的数组，而是结构体、类怎么办，下面给出一些方法和例子。

方法一：定义比较函数（最常用）

```c++
//情况一：数组排列
int A[100];
bool cmp1(int a,int b)//int为数组数据类型
{
return a>b;//降序排列
//return a<b;//默认的升序排列
}
sort(A,A+100,cmp1);
```



```c++
//情况二：结构体排序
Student Stu[100];
bool cmp2(Student a,Student b)
{
return a.id>b.id;//按照学号降序排列
//return a.id<b.id;//按照学号升序排列
}
sort(Stu,Stu+100,cmp2);
```

注：比较方法也可以放在结构体中或类中定义。

方法二：使用标准库函数

另外，其实我们还可以再懒一点，在标准库中已经有现成的。它在哪呢？答案是functional，我们include进来试试看。functional提供了一堆基于模板的比较函数对象，它们是：**equal_to<Type>、not_equal_to<Type>、greater<Type>、greater_equal<Type>、less<Type>、less_equal<Type>**。这些东西的用法看名字就知道了。在这里，我么sort要用到的也只是greater和less就足够了，用法如下：

● 升序：sort(begin,end,less<data-type>())

● 降序：sort(begin,end,greater<data-type>())

缺点：也只是实现简单的排序，结构体不适用。

**#include <iostream>**
**#include <cstdio>**
**#include <algorithm>**
**#include <functional>**
**using namespace std;** 

//简单使用方法
sort(A,A+100,greater<int>());//降序排列
sort(A,A+100,less<int>());//升序排列
方法三：重载结构体或类的比较运算符

```c++
//情况一：在结构体内部重载
typedef struct Student{
int id;
string name;
double grade;

bool operator<(const Student& s)
{
return id>s.id;//降序排列
//return id<s.id;//升序排列
}
};
vector<Student> V;
sort(V.begin(),V.end());
```

```c++
//情况二：在外部重载
vector<Student> V;
bool operator<(const Student& s1, const Student& s2)
{
return s1.id>s2.id;//降序排列
//return s1.id<s2.id;//升序排列
}
sort(V.begin(),V.end());
```


注意：一定要重载<运算符，因为系统默认是降序，用的是<运算符。

方法四：声明比较类（少用）

```c++
struct Less
{
bool operator()(const Student& s1, const Student& s2)
{
return s1.id<s2.id; //升序排列
}
};
sort(sutVector.begin(),stuVector.end(),Less());
```


一个list（链表）使用sort()实例：

```c++
#include "stdafx.h"
#include <iostream>
#include <list>
#include <numeric>
#include <algorithm>
#include "stdlib.h"
#include <stdio.h>

using namespace std;

//给list起一个别名LISTINT
typedef list<int> LISTINT;
//再起一个别名 LISTCHAR
typedef list<int> LISTCHAR;

int _tmain(int argc, _TCHAR* argv[])
{
//用list容器处理整型数据
//用LISTINT创建一个名为listOne的list对象
LISTINT listOne;
//声明i为迭代器
LISTINT::iterator i;

//从前面向listOne容器中添加数据
listOne.push_front (2);
listOne.push_front (1);

//从队尾向listOne容器中添加数据

listOne.push_back (5);
listOne.push_back (4);
listOne.push_back (9);
listOne.push_back (7);
listOne.push_back (12);

//从前向后显示listOne中的数据,排序前的链表
cout<<"listOne.begin()--- listOne.end():"<<endl;
for (i = listOne.begin(); i != listOne.end(); ++i)
cout << *i << " ";
cout << endl;

listOne.sort(); //用sort()函数排序,默认升序

//排序完毕后的列表
cout<<"listOne.begin()--- listOne.end():"<<endl;
for (i = listOne.begin(); i != listOne.end(); ++i)
cout << *i << " ";
cout << endl;

system("pause"); //按任意键后退出
return 0;
}
```

