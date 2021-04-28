---
title: C++ note
hide: false
math: false
date: 2021-03-23 23:54:32
categories: Language
tags:
index_img:
banner_img:
comment: true
excerpt: 随便记的C++笔记
---

​	

<!-- more -->

## class

* 类中定义函数默认为inline
* 类默认修饰符为private
* 派生类**继承**基类默认为private
* 访问修饰符

```c++
public:			//可外部访问
private: 		//只有类函数和友元函数可访问
protected:	//外部不可访问，派生类中可访问
```

* 派生类中访问修饰符

| 继承基类方式 | public    | private | protected |
| ------------ | --------- | ------- | --------- |
| public       | public    | private | protected |
| private      | private   | private | private   |
| protected    | protected | private | protected |

##### friend友元

* 友元可访问类中的私有和protected变量

* 破坏封装性，提高运行效率
* 友元没有继承性，没有传递性，友元单向
* 友元函数

```c++
class A{
  int a;
  friend int geta(A tmp);
};

itn geta(A tmp){ return A.a; }
```

* 友元类

```c++
class A{
	int a;
	friend class B;
};
class B{
public:
  int geta(A tmp){ return tmp.a; }
};
```

##### const函数只能访问const对象或函数

```c++
class Apple{
public:
    const int apple_number;
    void take(int num) const;
    int add(int num);
};
Apple::take(int num) const{ //只能访问const变量/函数
  cout << num << endl;
}
Apple::add(int num){//可以访问所有类对象/函数
  take(num);
}
```

##### static函数只能访问static对象或函数，static成员函数没有this指针

```c++
class Apple { 
    public:
        static void printMsg() {
            cout<<"Welcome to Apple!"; 
        }
};
int main(){
  Apple::printMsg();
  return 0;
}
```

##### 派生类

* 派生类不会继承基类运算符重载、友元函数、构造函数、析构函数

##### virtual

* 虚函数的调用取决于指向或者引用的对象的类型，而不是指针或者引用自身的类型；
* 默认参数静态绑定，默认参数的使用需要看指针或者应用本身的类型，而不是对象的类型(与上一条正好相反)；

## const

* 多文件需访问const需显示声明extern const

* const指针就近修饰

```c++
const int * p; 
/*指向const int类型的指针
不能通过指针来修改对象的值
若指向非const变量，也不能修改变量值
*/
    
int * const p = &i;
/*
常指针，必须初始化
不能修改指针，但是可以修改指针指向的变量
*/
```

  * 对于非内部数据类型的输入参数，应该将“值传递”的方式改为“const 引用传递”，目的是提高效率。例如将void func(A a) 改为void func(const A &a)。

## static

* static只会被初始化一次
* static生命周期贯穿程序始终
* static没有this指针，无法访问vptr

## inline

* 在编译阶段编译器内联：
  * 会采用内联的函数：编译阶段能确定的函数
  * 不会采用内联的函数：编译阶段无法确定的函数
    * eg. virtual函数呈现多态性时无法内联
* 原理：复制
* 不一采用内联函数：
  * 函数内部代码较长，复制后消耗较多内存；
  * 函数内部有循环，执行函数时间比调用函数大(无需使用内联)。
* 应使用内联：
  * 函数代码较短
  * 函数调用时间大于执行时间

## 运算符重载

## volatile

* 用volatile声明的变量可以被编译器未知的事件修改
* volatile一般用于多线程
* volatile告诉编译器
  * 不对变量优化
  * 每次对变量读写都要从内存取出，而非使用寄存器

```c++
#include <stdio.h>

int main(void)
{
    const volatile int local = 10; //使用volatile声明，可以被修改
    int *ptr = (int*) &local;

    printf("Initial value of local : %d \n", local);
    *ptr = 100;//有效修改了*ptr
    printf("Modified value of local: %d \n", local); //100
    return 0;
}
/******************************/
int main(void)
{
    const int local = 10;
    int *ptr = (int*) &local;

    printf("Initial value of local : %d \n", local);
    *ptr = 100; //无效，无法修改*ptr
    printf("Modified value of local: %d \n", local);
    return 0;
}
```

## assert

* assert是宏，定义在 

```c
assert.h
```

* 若assert条件错误，则终止(Abort)程序
* 用于检测逻辑不可能情况
* 若要忽略断言，加上:

```c
#define NDEBUG //要加载assert.h之前，不然会重复#define NDEBUG
//#include <assert.h>
```

## extern

* c++调用c函数

```c++
extern "C"{ //只在c++中支持，所有extern “C”的声明都要放在cpp中
  #include "tmp.h"
  int foo(int);
}
```

* 要调用其它文件中的变量、函数需要在变量声明前加extern修饰

## struct

* C与C++中struct区别

|                  | c                  | c++                                  |
| :--------------- | :----------------- | ------------------------------------ |
| 结构体内声明函数 | 不能声明函数       | 能声明函数                           |
| 使用访问修饰符   | 不能使用访问修饰符 | 可以使用：public、protected、private |
| 定义结构体       | 必须加struct       | 不需要加struct                       |
| 继承             | 无继承             | 可以继承，可以使用virtual            |
| 结构体和函数同名 | 可以正常运行调用   | 定义结构体必须带struct               |

* 与Class的区别
  * struct默认访问是public
  * struct用于数据结构实体
  * class用于对象实体

## Lambda表达式(匿名函数)

## Using

* 同等于typedef

```c++
using VI = vector<int>; //equal to typedef vector<int> VI
```

* using 指定在之后的作用域中使用该命名空间

```c++
#include <iostream>
using namespace std; //之后使用std命名空间
//using namespace std::cout; //之后使用std::cout函数

class Base{
    public:
        void f(){ cout<<"f()"<<endl;}
        void f(int n){cout<<"Base::f(int)"<<endl;}
};
class Derived : private Base {
    public:
        using Base::f; //不带形参，使用基类的函数
        void f(int n){cout<<"Derived::f(int)"<<endl;}
};

int main() {
    Base b;
    Derived d;
    d.f(); //f()
    d.f(1);//Derived::f(int)
    return 0;
}
```

## dynamic_cast & static_cast

* 强制类型转换，用法：

```c++
static_cast<new_type>(e);
dynamic_cast<new_type>(e);
```
