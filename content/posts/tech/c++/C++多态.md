---
title: "C++多态"
date: 2024-03-23
lastmod: 2024-03-23
author: "zhaohan"
tags:
  - C++
  - 计算机基础
---

# 1 多态定义

在基类的函数前加上virtual关键字，在派生类中重写该函数，运行时将会根据对象的实际类型来调用相应的函数。如果对象类型是派生类，就调用派生类的函数；如果对象类型是基类，就调用基类的函数。

# 2 虚函数表和虚函数指针

每一个有虚函数的类（或有虚函数的类的派生类）都有一个虚函数表，该类的任何对象中都放着虚函数表的指针。虚函数表中列出了该类的虚函数地址。

```cpp
class Base {
public:
    virtual void func() { cout << "Base::func() called" << endl; }
};

class Derived : public Base {
public:
    void func() override { cout << "Derived::func() called" << endl; }
};
```
对于Base类，编译器会生成一个虚函数表，包含指向Base::func的指针。对于Derived类，编译器也会生成一个虚函数表，但这个表中func函数的条目会指向Derived::func。当创建Derived类的对象并通过Base类的指针或引用调用func时，程序会运行Derived::func，因为Derived对象中的虚函数指针指向Derived的虚函数表，而这个表中func的条目指向Derived::func。


# 3 构造函数和析构函数

构造函数一定不是虚函数： 当定义一个对象的时候，首先会分配对象内存空间，然后调用构造函数来初始化对象。vptr变量是在构造函数中进行初始化的。又因为执行虚函数需要通过vptr指针来调用。

析构函数一定是虚函数：通过基类的指针删除派生类对象时，通常情况下只调用基类的析构函数，这就会存在派生类对象的析构函数没有调用到，存在资源泄露的情况。解决办法是把基类的析构函数声明为virtual。


