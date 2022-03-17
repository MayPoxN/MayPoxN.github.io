---
title: Some C/C++ Explain At an Oral Quiz
date: 2022-03-11 20:58:02
tags:
- C/C++
categories: 
- 小知识
# password: Nefelibata 
---

# 一、虚函数的数据结构及工作原理

虚函数：用 [virtual](https://blog.csdn.net/hackbuteer1/article/details/7558868) 定义的函数。

虚函数的来源：基于 C++ 可以将子类转换为父类的特性。

```c++
CBsaic		*parent;
CBasic		*p1;
CChildren	*child;

parent = new CBasic;
child = new CChildren;
p1 = new CChildren;
```

p1 是 CBsaic 类型的指针，但实例化的对象是 CChildren，若子类和父类有一样的函数，那么 p1 会调用子类函数还是父类函数？基于这个问题，C++ 给出了多态的概念：根据实际对象的类型决定函数调用的具体目标，使用 virtual 关键字对多态进行支持。**被 virtual 声明的函数被重写后具有多态性。**

底层机制：虚函数通过使用**虚函数表**和**虚函数表指针**实现。虚函数表是一个类中虚函数的地址，用于索引类本身和类的虚函数，若子类重写了父类的函数，则会在相应的虚函数表处替换成为子类虚函数的地址。虚函数表指针则存在于每一个对象中，它指向对象所对应 类的虚函数地址。

构造函数是不是虚函数影响并不大，因为在构造子类之前一定要先构造父类。在**存在继承**并且构造函数需要用来**析构资源**时，构造函数就必须为虚函数。此时，若使用父类指针指向子类，用 delete 构造函数时，只会调用父类，并不会调用子类，这时会照成内存泄漏。

# 二、CONST 同 DEFINE 的区别

- CONST 在编译时就会确定他的值；DEFINE 则在预处理时进行替换。
- CONST 是有数据类型的，在编译时需要检查；DEFINE 则没有数据类型，也不需要检查。
- CONST 在静态存储区存储，并且仅存一分；DEFINE 则在代码段区，每一次替换都会进行一次拷贝。
- DEFINE 可以用来防止重复的定义，CONST 则不行。

# 三、指针与引用

- 指针是一个变量，存储的内容是一个地址；引用是已有对象的别名。
- 指针是一个实体，需要分配内存；引用仅是变量的别名，不需要分配。
- 指针可以套娃；引用不可以。
- 指针和引用的自增运算结果不同。
- 指针是间接访问，引用是直接访问。
- 指针可以不用初始化，引用则必须初始化。

# 四、指针与数据

1. 数组对应一块内存；指针则指向一块内存。
2. 数组的地址和空间大小在生命周期中不会变，但内容可能改变；而指针指向的内存大小可以随时改变。
3. 指针指向常量字符串时，其内容不可更改。
4. sizeof() 函数可以计算出数组元素的个数；但不可以计算指针所指的内存大小。
5. 数组名是常量指针；指针是变量指针。
6. 对数组引用& 和对指针引用& 意义不同。此时数组名不再当成指向一个元素的常量指针。

# 五、不用额外空间实现变量交换

```c++
// 用我最喜欢的异或方式，几乎可以交换任意数据。
void Switch_Xor(int *p1, int *p2){
	*p1 = *p1 ^ *p2;
	*p2 = *p1 ^ *p2;
	*p1 = *p1 ^ *p2;
}
```

# 六、函数指针与指针函数

- 函数指针：函数指针是**指向函数的指针**，是指针变量，和函数名无关，只与参数列表和返回类型有关。
- 函数指针：本质是一个函数，返回值是一个指针。

```c++
#include <iostream>
using namespace std;

int ADD(int a, int b){
	return a + b;
}

int Sub(int a, int b){
	return a - b;
}

int* add(int *p1, int *p2){
	int a = *p1 + *p2;
	int *p = &a;
	return p;
}

int main(){
	int a = 5, b = 3;
	int *p1 = &a;
	int *p2 = &b;

	// 声明一个函数指针
	// 只能指向有两个int参数，返回值为int的函数
	int (*funp)(int,int);

	funp = ADD;
	cout <<"函数指针指向ADD函数计算结果：" <<funp(a, b) << endl;
	funp = Sub;
	cout << "函数指针指向Sub函数计算结果：" << funp(a, b) << endl;

	cout << "指针函数计算结果：" <<*add(p1, p2) << endl;
	return 0;
}
```

# 七、C++ 源文件文本到可执行文件的过程

1. 预处理：对所有的 DEFINE 进行宏替换；处理所有的条件编译 #IFDEF、#PRAGMA 等；处理 #INCLUDE 指令；删除注释。
2. 编译：将预处理后的文件进行词法分析、语法分析、语义分析。
3. 优化性能。
4. 汇编：将汇编文件转为机器代码。
5. 链接：包括地址和空间分配，符号决议和重定位。

# 八、C++11 新特性

1. NULLPTR 代替 NULL，传统 C++ 在识别 NULL 有两种情况。一种是空指针，另一种是当成0。在重载时往往需要把指针当成0去处理。
2. 类型推导：auto 和 decltype，可以让编译器找出表达式的类型。
3. 区间迭代：使得 C++ 的 for 语句同 python 一样便捷。
4. 初始化列表。
5. 模板增强。
6. 新增容器：STD::array，STD::forward_list（单链表）
7. 正则表达式。
8. 线程支持。
9. [右值引用](http://c.biancheng.net/view/7829.html)。

# 九、C++ 与 C 的区别

1. C 语言只要面向过程设计，核心为数据结构和算法，具有高效性。主要考虑一个过程，对输入处理并得到一个输出。
2. C++ 是面向对象的设计，C++ 首先考虑如何构造一个对象模型，让这个 Solution 符合问题，通过对象信息得到输出。
3. C++ 增强点：
   1. 命名空间
   2. 实用性
   3. register 关键字
   4. 变量监测加强
   5. STRUCT加强

# 十、malloc 原理

函数原型：void* malloc(size_t n) 返回值类型为 void*，为动态分配得到的内存，但大小是确定的，不可以越界。



malloc 函数的实质是它可以将可用内存块通过链表的方式连接成一个长列表。当 malloc 被调用时，它沿着列表去寻找一个大到可以满足需求的内存。并将内存一分为二，一部分分配给用户，另一部分返回列表内。

# 十一、内存泄漏、野指针

内存泄漏：动态申请的内存空间没有被正常释放，同时也无法被继续使用的情况。

野指针：指向被释放的内存或者访问受限的指针。

野指针的起因：

1. 指针未初始化
2. 被释放的指针没有被置位NULL
3. 指针越界操作

解决内存泄漏的方法：使用[智能指针](http://c.biancheng.net/view/7909.html)。

# 十二、STATIC

## 1、局部静态变量：STATIC 局部变量和普通局部变量的区别？

- STATIC 局部变量只初始化一次，下一次依据上一次的结果值。
- 程序的局部变量存在于堆栈中。
- 全局变量存在于静态区中。
- 动态申请的数据存在于堆中。

## 2、全局静态变量：STATIC 全局变量和普通全局变量的区别？

- 全局变量用 STATIC 修饰就构成了静态的全局变量。
- 全局变量和静态的全局变量都是静态存储方式。
- （非静态）全局变量的作用域是整个源程序，可以跨文件作用。
- **静态的全局变量则只在定义该变量的源文件内有效**，只能被该源文件内的函数公用，不可以跨文件。这一特点可以避免在其他源文件中引起错误。

## 3、静态成员函数：STATIC 函数与普通函数的区别？

- STATIC 函数和普通函数的作用域不同。
- 普通函数默认是 extern 的，可以被别的代码文件调用。
- STATIC 函数只可以在本源码文件中被调用，不可以跨文件调用。基于这一特性，可以避免相同名字函数的冲突。
- STATIC 函数在内存中只有一份，普通函数则在每次调用中维持一份拷贝。

# 十三、UNION 与 STRUCT

[UNION 和 STRUCT 有着本质上的区别。](https://blog.csdn.net/liguangxianbin/article/details/80510669)

1. 在存储信息时，STRUCT 可以存储多个成员，而 UNION 每个成员会共享一个存储空间，只能存最后同一个成员。
2. 在任何时刻，UNION 只存放被选中的那个成员，STRUCT 每个成员都在。
3. 对 UNION 的不同成员复制，将会对其他成员重写。

- STRUCT 中所有变量是"共存"的——优点是"有容乃大"，全面；缺点是 STRUCT 内存空间的分配是粗放的，不管用不用，全分配。


- UNION 中是各变量是"互斥"的——缺点就是不够"包容"；但优点是内存使用更为精细灵活，也节省了内存空间。

STRUCT 会遵循**对齐规则**去存储数据。而 UNION 则按单个成员最大需求去分配，并且每个成员都共享首地址。

特殊玩法：STRUCT 可以套娃，可以自己套自己，也可以套别人；UNION 则是分配给你一块内存，你随便玩。

# 十四、new 与 malloc

1. 属性：new 是关键字；malloc 是库函数，需要头文件支持。
2. 参数：new 申请内存无需指定大小，编译器会自行计算；malloc 则需要我们给出。（常用sizeof函数）
3. 返回类型：new 分配成功返回的是对象类型的指针，和对象严格匹配并不需要类型转换，因此 new 操作是安全的；malloc返回的则是void*。
4. 分配失败：new 分配失败则会抛出 bad_alloc 异常；malloc 则会返回NULL。
5. 重载。
6. 内存区域：new 分配的内存在自由存储区；malloc 则在堆上分配内存。

# 十五、C++ 类型转换

1. static_cast
2. dynamic_cast
3. const_cast
4. reinterpret_cast

```c++
#include<iostream>
//using namespace std;
//static_cast<typeid>（）其中typeid可以为一般类型，也可以为指针引用

class A{
public:
    // 构造函数初始化列表
    // http://c.biancheng.net/view/2223.html
	A() :i(1), j(1) {}
	~A() {}

	void printA(){
		std::cout << "call printA() in class A" << std::endl;
	}

	void printSum(){
		std::cout << "Sum = " << i + j << std::endl;
	}
private:
	int  i, j;
};


class B :public A{
public:
	B():a(2),b(3){}
	~B(){}

	void printB(){
		std::cout << "call printB() in class B" << std::endl;
	}

	void printSum(){
		std::cout << "Sum = " << a + b << std::endl;
	}

	void Add(){
		a++;
		b++;
	}
private:
	double a, b;
};


int main(){
	B *ptrB = new B;          //创建一个B类型对象。堆区
	ptrB->printSum();         //输出和

	A *ptrA = static_cast<A*>(ptrB);  //将派生类转化成父类，上行转换
	ptrA->printA();
	ptrA->printSum();

	ptrA = new A;                 //创建一个父类对象
	ptrB = static_cast<B*>(ptrA); //将父类对象转换成子类对象，下行转换
	ptrB->printB();
	ptrB->printSum();


	B b;                         //栈上创建一个B类型对象
	B &rB = b;                   //对b的引用
	rB.printSum();
	A &rA = static_cast<A &>(b); //派生类转换成基类，上行转换
	rA.printA();
	rA.printSum();

	A a;
	A &rA1 = a;
	rA1.printA();
	B &rB1 = static_cast<B &>(a); //将基类转换成派生类，下行转换
	rB1.printB();
	rB1.printSum();

	system("pause");
	return 0;
}
```

# 十六、面向对象

## 1、面向对象

面向对象把数据和对数据的操作方法放在一起，作为一个相互依靠的整体，称之为对象。对同类对象抽象出共同特效，类中大多数数据只能用本类的方法进行处理。

## 2、面向对象三大特性

1. 封装：将一类事物的属性和行为抽象为一个类，使属性私有化、行为公开化，提高数据隐蔽性，复用性高。
2. 继承：进一步将属性和行为抽象为一个父类，而每一个子类拥有父类的行为和属性，也有自己的行为和属性。
3. 多态：[接口复用。](https://www.runoob.com/cplusplus/cpp-polymorphism.html)当类之间存在层次结构，并且类之间是通过继承关联时，就会用到多态。

这里涉及到 virtual 修饰符。例如父类是形状，有面积方法；父类派生出三角形和四边形两个子类，这两个子类的面积方法不一样，各有各的。这就是多态。

# 十七、前置与后置

- 前置 ++ 的实现比较高效，自增之后直接返回 *this 指针即可。
- 后置 ++ 实现比较麻烦，需要返回自增之前的对象。需要先将对象拷贝一份，然后再自增，最后返回那个拷贝。

# 十八、静态库和动态库

静态库：

1. 链接时将函数放进可执行的程序中
2. 可以产生多个副本
3. 不依赖程序运行

动态库：

1. 程序运行后在加载时才会去动态库找函数
2. 多线程共享
3. 依赖程序运行

# 十九、STRUCT 内存大小的确定

这一点之前说过了，STRUCT 遵循对齐规则。

- 对于 32 位机器，是 4 字节对齐。
- 对于 64 位机器，是 8 字节对齐。

```c++
struct A{
    int a;
    char b;
    int c;
};
```

以上代码的所分配的内存为：4 + 4 + 4 = 12 字节

# 二十、strlen()与strcpy()——[未完成]

```c++
#include<iostream>
#include"test.h"
using namespace std;
//将src字符串复制到desc中
char* mystrcpy(char* desc, const char* src)
{
    if (desc == NULL || src == NULL) //内存正常判断
    {
        return NULL;
    }
    char* str = desc;
    while ( (*str = *src) != '\0')//‘\0'字符串结束标志
    {
        str++;
        src++;
    }
    return desc;
}
 
 
//将src字符串连接到desc中
char* mystrcat(char* desc, const char* src)
{
    if (desc == NULL || src == NULL)
    {
        return NULL;
    }
    char* str = desc;
    while (*src++ != '\0'); // 第一步：找出目的字符串的结尾
    src--;
    while ((*desc++ = *src++) != '\0');//第二步：将源字符串添加到目的字符串
    return str;
}
 
 
//将src字符串前n个字符复制到desc中
char* mystrncpy1(char* desc, const char* src, int n)
{
    char* str = desc;
    //前面应该有个目的内存是否不足判断
    int m = 0;
    while (*str != '\0')
    {
        str++;
        m++;
    }
 
 
    if (desc == NULL || src == NULL || n>m ) //内存正常判断
    {
        return NULL;
    }
 
 
    while (n--)
    {
        *str++ = *src++;
    }
    return desc;
}
 
 
//比较两个字符串大小
int mystrcmp(const char* str1, const char* str2)
{
    while (*str1 && *str2 && *str1 == *str2)
    {
        str1++;
        str2++;
    }
    return *str1 - *str2;
}
 
 
//求出字符串str长度
int mystrlength(const char* src)
{
    if (src == NULL)
    {
        return 0;
    }
 
 
    int n = 0;
    while (*src++ != '\0')
    {
        n++;
    }
    return n;
}
```

# 二十一、memcpy()与memset()——[未完成]

```c++
#include<iostream>
using namespace std;
/*
memcpy函数用于资源内存（src指向的内存）拷贝到目标内存（desc指向的内存）；拷贝的个数size
用法：：(1)可以拷贝任何类型的对象，因为函数的参数类型是void* ,由于函数拷贝是一个字节一个
      字节拷贝，实际操作是将void*强制转换成了char*，这样才能保证每一次加一个指针
*/
 
 
//实现，未考虑内存重叠情况
void* memcpy1(void * desc, const void * src, size_t size)
{
    if (desc == NULL && src == NULL)
    {
        return NULL;
    }
    unsigned char* desc1 = (unsigned char*)desc; //将void*转成unsigned char*类型
    unsigned char* src1 = (unsigned char*)src;   //将void*转成unsigned char*类型
 
 
    while (size-->0)
    {
        *desc1++ = *src1++;
    }
    return desc;  
}
 
 
//考虑内存重叠的情况
void* memcpy2(void* desc, const void * src, size_t size)
{
    if(desc == NULL && src == NULL)
    {
        return NULL;
    }
    unsigned char* desc1 = (unsigned char*)desc;
    unsigned char* src1 = (unsigned char*)src;
    //当内存重叠时，从后往前复制
    if(desc > src && desc1 < (src1 + size))//内存发生重叠
    { 
        for (int i = size - 1; i >= 0; i--)
        {
            *desc1++ = *src1++;
        }
    }
    else
    {
        for (int i = 0; i < size; i++)
        {
            *desc1++ = *src1++;
        }
    }
    return desc;
}
 
 
//memset内部实现memset(void*s,int ch,size_t n)
/*将s所指向的某一块内存中的前n个字节的内容全部设置为ch指定的ASCII值，返回s*/
void * memset1(void * ptr, int value, size_t n)
{
    if (ptr == NULL && n < 0)
    {
        cout << "赋值出现错误" << endl;
        return;
    }
    char *s = (char*)ptr; //定义一个指针来接str；辅助指针
    while (n--)
    {
        *s++ = (char)value;
    }
    return ptr;
}
 
 
 
//下面就是根据源码而来的memset实现：
 
void* memset(void* dst,int val, size_t count)
{
    void* ret = dst;
    while(count--)
    {
        *(char*)dst = (char)val;
        dst = (char*)dst + 1; 
    }
    return ret;
}
```

# 二十二、C/C++ 内存管理与分配

在 C++ 中，内存分为5个区，分别是堆、栈、自由存储器、全局 / 静态存储区、常量存储区。

## 1、栈

在执行程序的过程中，局部作用关于出现一些局部变量可以在栈上创建，等脱离该作用域时，创建的内存就会被释放。

栈是机器系统提供的数据结构，计算机在底层对栈提供了支持：

1. 有专用寄存器存放栈的地址
2. 入栈、出栈有专门的指令执行

因此，栈的效率很高。

## 2、堆

堆是用语程序内存对台分配的，例如 C/C++ 中的 new / malloc 分配，delete / free 释放。

堆是 C/C++ 函数库提供的，其实现机制很复杂。

## 3、自由存储区

自由存储区是 C++ 基于 new 操作符的一个概念，凡是通过 new 申请的内存就是自由存储区。

## 4、全局 / 静态存储区

全局 / 静态存储区在程序编译期间就已经分配好，在程序整个运行阶段一直存在。

## 5、常量存储区

常量存储区是一块特殊的内存，里面存放的是不允许修改的常量。

# 二十三、深拷贝与浅拷贝

- 浅拷贝：只拷贝了基本的数据类型，而引用数据类型，复制后也是会发生引用。浅拷贝只是指向被复制的内存地址，如果原来的对象被修改，那么浅拷贝出来的也会被修改。
- 深拷贝：在计算机中重新开辟一块新内存，将原来的对象复制过来。常用 new 或者 malloc 等。

# 二十四、debug 与 release

- debug：通常被称为调试版本，包含着调试信息，便于程序员调试。
- release：通常成为发布版本，往往是经过各种优化，是的程序在代码大小和运行熟读是最优的，方便用户使用。

# 二十五、MAIN 的返回值

MAIN 函数的返回值用于说明程序的退出状态。

如果返回0，说明程序正常退出，否则程序是异常退出。

有的编译器会自动在目标文件中添加 return 0; 语句。

# 二十六、C++ 动态链接库与 C 动态链接库

- C 调用 C++ 动态链接库：需要用先用 C++ 将库中的类进行 API 封装。
- C++ 调用 C 动态链接库：需要加上 extern C，用 C 编译准则来编译。

# 二十七、结构体

- C++中：结构体可以通过重载操作符 "==" 来进行结构体的比较，但要注意内存对齐。为了提高 CPU 效率，通常用宏定义来改变内存对齐方式。
- C中：结构体无法比较，且因为内存对齐，空白部分会有垃圾填充造成干扰。

# 二十八、拷贝构造函数为什么传递引用——[未完成]

[原因](https://www.cnblogs.com/chio/archive/2007/09/14/893299.html)：参数为引用，不传递值是为了防止拷贝构造函数的无限递归，这会导致栈溢出。这也是编译器的强制要求。

---

~~***未审核部分***~~

```c++
#include<iostream>
 
class Example
{
private:
	int m_test;
public:
 
	//带参数的构造函数
	Example(int x) :m_test(x)
	{
		std::cout << "constructor with argument!" << std::endl;
	}
 
	//拷贝构造函数
	Example(const Example &test)
	{
		m_test = test.m_test;
		std::cout << "copy constructor" << std::endl;
	}
 
	//赋值运算符重载
	Example&  operator=(const Example &test)
	{
		std::cout << "assignment operator" << std::endl;
		m_test = test.m_test;
		return *this;
	}
 
	void Print(Example test)
	{
 
	}
};
 
int main()
{
	Example aaa(2);
	Example bbb(3);
 
	bbb = aaa;
 
	Example ccc = aaa;
	Example ddd(aaa);
	bbb.Print(aaa);
 
	system("pause");
	return 0;
}
```

```c++
constructor with argument!  // Example aaa(2);
constructor with argument!  // Example bbb(3);
assignment  operator        // bbb = aaa;
copy constructor            // Example ccc = aaa;
copy constructor            // Example ddd(aaa);
copy constructor            // bbb.Print(aaa);
```

第一二就不解释了

至于第三个，bbb是已经实例化的对象，不需要构造，因此只会调用=运算符重载函数，第四个的ccc还没有实例化，因此要调用拷贝构造函数，构造出ccc，而不是=运算符重载函数

第五个就是拷贝构造函数的应用，

第六个：实际是将aaa作为参数传递给bbb.Print(Example  ex)，即Example  ex = aaa，和第四个一致，所以还是调用拷贝构造函数。

通过这个例子， 我们来分析一下为什么拷贝构造函数的参数只能使用引用类型。

看第四个输出： copy constructor                      //CExample ccc = aaa;

构造ccc，实质上是ccc.CExample(aaa); 我们假如拷贝构造函数参数不是引用类型的话， 那么将使得 ccc.CExample(aaa)变成aaa传值给ccc.CExample(CExample ex)，即CExample ex = aaa，因为 ex 没有被初始化， 所以 CExample ex = aaa 继续调用拷贝构造函数，接下来的是构造ex，也就是 ex.CExample(aaa)，必然又会有aaa传给CExample(CExample ex), 即 CExample ex = aaa;那么又会触发拷贝构造函数，就这下永远的递归下去。

---

# 二十九、程序崩溃原因

- 读取未赋值的变量
- 函数栈溢出
- 数组越界
- 指针目标对象不可用

# 三十、C++ 字符串输入——[未完成]

1. cin>>
2. cin.get()
3. cin.getline()
4. getline()
5. gets()
6. getchar()

# 三十一、LAMBDA 表达式

一个 [lambda](http://c.biancheng.net/view/7818.html) 表达式表示一个可调用的代码单元，我们可以将其理解为一个未命名的[内联函数](http://c.biancheng.net/view/199.html)。

lambda 和普通函数一样，具有返回类型、参数列表、函数体。但lambda 可以定义在函数内部。其定义如下：

\[capture list 捕获列表](parameter list 参数列表){函数体}

```c++
auto f = [](const string &a, const string &b){return a.size() < b.size()}
```

我们可以忽略参数列表和返回类型，但永远包含捕获列表和函数体。

其中，捕获列表通常为空列表。

# 三十二、C++ 编程优化——[未完成]

\1. 对齐原则。比如64位总线，每次寻址读取8B。编程时注意变量地址，尽量消耗总线最少的寻址次数。堆内存申请时，系统严格按照对齐原则分配，故而使用时候也尽量不要跨寻址边界。

\2. 需要的时候，可为了效率拷贝代码，虽然增加了代码体积，但这是值得的。尤其是for循环，若次数比较少，拆开亦无妨。

\3. 位运算中，-1右移，左边补1，故仍为-1；-1左移，右边补0，故不再为-1。

\4. 每次申请的堆内存，最好初始化，里面是垃圾数据，而并非为空。

\5. 项目开发中，往往一个引擎对外暴露的是一个纯虚类，而其接口就是这个类的**指针变量。

\6. 程序逻辑，重在语义。不能为代码的过分简单而减少函数的设计。

\7. *&表示对指针的引用。

\8. 类的静态方法不可调用其非静态方法，亦不可调用非静态成员变量。

\9. 多文件编程时，头文件不可相互包含。

\10. 头文件里尽量不要使用using namespace std;

\11. static成员定义要放在cpp文件里面，而不是头文件里。

\12. 纯虚类尽量不要延续两层以上。

\13. include引用尽量都放在cpp文件里。

\14. 子类继承父类，不可在构造函数里初始化父类并未在构造函数里初始化的成员，也就是说，子类构造函数里能初始化的成员，只有自己本身的和父类构造函数里的。

\15. 项目开发中，对于一些依赖本地环境的参数，要写专门的配置文件，比如服务器地址。

\16. 头文件里只声明，不定义。在头文件中，全局变量声明，必须加extern修饰；静态成员变量声明放在头文件，定义放在cpp文件，若是普通静态变量，最好声明和定义放在cpp，因为static作用域限于单文件，放在cpp里只对本文件可见，放在头文件会被所有引用该头文件的cpp拷贝一份完全相同的变量。

\17. linux下，进行文件操作时，文件路径要采用绝对路径（相对路径很多时候会出bug），文件指针要对其返回值作判断，防止空指针。

\18. debug状态下使用assert是极好的，不过记得发布版本前在#include <cassert>前加上#define NDEBUG，assert语句会被NDEBUG所影响。这里多嘴一句，错误与异常是不同的，异常是不可避免，在发布版本里不可或缺的，故而assert不能用于处理异常。注：在加上#define NDEBUG后，不论是调试还是运行，assert语句都会被直接忽略，故而平时开发时把#define NDEBUG注释掉，发布时再启用它。

\19. 面向对象编程：可维护，可复用，可扩展，灵活性好。

\20. 频繁使用的代码里，尽量少使用容器，因为其创建和回收的开销很大。

\21. 字符串拼接效率：[经过测试](http://bbs.csdn.net/topics/360000434)，memcpy效率最高，string类的+=效率特别高，足以满足日常所需，而strcat效率很差，append也不快。

\22. 基类中的虚函数要么实现，要么是纯虚函数（绝对不允许声明不实现，也不纯虚）。

\23. 在C++的类中，普通成员函数不能作为pthread_create的线程函数，如果要作为pthread_create中的线程函数，必须是static。

\24. 当多个线程访问同一个类静态方法时，若该方法里只操作栈上数据，则无妨，因为线程的栈是独立的；若该方法操作了非栈上的数据（比如堆、全局变量等），则需要互斥锁。

\25. 内联函数，定义体要放在头文件，以保证每一个引用该头文件的cpp里都是完全相同的拷贝；inline关键字置于定义前，不必放在声明前；若定义在类内部，可不需要inline关键字。

\26. vector执行clear时，会自动调用元素（如果是类的话）的析构函数。

\27. 编程时，对变量（尤其是全局性质的变量或类）命名，要用解释性的，而不能用随意的j1,i1,n,m等名称，容易与库里的变量冲突。

\28. 定义宏时，尽量多用整数，少用-1，-2之类，容易受uint和int不统一带来的困扰。

\29. 函数形参采用默认值时，要写在声明里，而不写定义里，这样方便调用其头文件的cpp看得到默认值。

\31. utf-8 中文编码 三个字节表示一个汉字

\32. 项目开发时，使用条件编译：

```c++
#define DEBUG
main()
{
#ifdef DEBUG
    printf("Debugging\n");
#else
    printf("Not debugging\n");
#endif
    printf("Running\n");
}
```

发布版本时，注释掉第一行。这种方式要比开大量注释来得方便。

\33. 关于c字符数组，需要注意一个初始化问题：

```c++
(1) char str[10]="";
(2) char str[10]={'\0'};
(3) char str[10]; str[0]='\0';
```

前两者的意义在于str的每个元素都初始化为'\0'，第三者仅仅初始化第一个元素。当数组长度很大时，前两者时间开销极大。故而，有些不必要的时候，不要如此初始化。

\34. 判断一个字符是否为数字：

头文件：#include <ctype.h>
定义函数：int isdigit(int c);
函数说明：检查参数 c 是否为阿拉伯数字0 到9。
返回值：若参数c 为阿拉伯数字，则返回true，否则返回null(0)。
附加说明：此为宏定义，非真正函数。

```c++
#include <ctype.h>
main(){
    char str[] = "123@#FDsP[e?";
    int i;
    for(i = 0; str[i] != 0; i++)
        if(isdigit(str[i]))
            printf("%c is an digit character\n", str[i]);
}
```

\35. 在类里声明静态常量，尤其是数组定义的时候，如static const *str = "sdfsssf", 要在const前加上constexpr修饰，可以在编译器就将“sdfsssf”赋值给str，达到类似宏的效果。

\36. std::string::find_last_of("/\\") 这里的"/\\"指的是'/'或'\'，在搜索字符时用得到。

\37. 项目里c字符串传递，多采用首地址+长度的方式，避免0x0存在导致的异常； 线程数要合适，大致为cpu总核数两倍以内为佳，线程间切换会一定程度上消耗程序性能。

　　有一个陷阱是在c字符串转string类型时，c字符串里如有0，转化时用strlen取长度就会出错。故而，c串表示尽量维护一个len来记录长度，而不是通过结尾0来判别。另外，strlen效率低且不安全，少用。

\38. 静态函数的实现写在h文件里；尽量把h文件里的函数实现前都加上inline，不论其复杂度，避免被多文件引用而引起重复定义错误。

\39. 静态成员变量必须在类外进行初始化。如果是复杂类型，比如容器，也要在类外定义，如std::vector<xx *> hj::sm_tr;

\40. 编程时，一般结构体里的堆内存由内存池管理申请或释放，或者stl里使用这些结构体作为元素时，使用其指针，而不是实例。因为stl里内存申请或释放，会调用其元素的构造和析构，这里会有陷阱。

\41. 使用set、map时，有一个陷阱，就是[]操作符。当使用这个操作符时，如果[x]里的x并不在set或map里，则stl会自动生成一个x元素，这样使得stl容器内容改变，如果开发者忽略了这点，后果可能无法预期。
