---
layout: post
title: C++ - 面向对象编程(OOP)
date: 2018-06-13
tag: [summary]
---

## 核心思想
* 数据抽象：将类接口与实现分离
* 继承(inheritance)：定义相似类型并对其相似关系建模
* 动态绑定(dynamic binding)：一定程度上忽略相似类型的区别，而以统一的方式使用它们的对象
	* 函数的运行版本由实参决定，即在运行时选择函数的版本，所以又称为运行时绑定(runtime binding)
	* 当使用基类的引用或者指针调用一个虚函数时将发生动态绑定
* 多态性(polymorphism)

## 基类与派生类
* 派生类
	* 类派生列表(class derivation list)指明是由哪个基类继承而来
	* 每个类控制它自己成员的初始化过程，首先初始化积累的部分，然后按照声明次序依次初始化派生类成员，最好遵循基类接口
	* 如果基类定义了一个静态成员，则整个继承体系中只存在该成员的唯一定义，只存在唯一实例
	* 添加`final`以避免之后继续继承C++11
	* 可以将基类的指针或引用绑定到派生类对象上
* 类型
	* 静态类型：编译时已知，变量声明时的类型或表达式生成的类型
	* 动态类型：变量或表达式标识的内存中的对象的类型
* 注意事项
	* 从派生类向基类的类型转换只对指针或引用有效
	* 基类向派生类不存在隐式类型转换
* 成员访问限定：主要看**子类、友元、外部**是否可以访问
	* `public`全部可以访问
	* `private`仅友元可以访问
	* `protected`子类、友元可以访问
* 继承关系
	* `public`：访问关系不改变，`public->public`，`protected->protected`，但`private`没了
	* `private`：`public->private`，`protected->private`
	* `protected`：`public->protected`，`protected->protected`

## 虚函数
* 对虚函数的调用在运行时才被解析
* 当且仅当对通过指针或引用调用虚函数时，才会在运行时解析该调用，也只有这种情况对象的动态类型才与静态类型不同
* 如果基类把一个函数声明为虚函数，则该函数在派生类中隐式地也是虚函数
* 通过添加`override`显式声明派生类的某一成员函数覆盖了基类的虚函数C++11，避免声明了独立的函数
* 回避虚函数的机制可通过使用作用域的方式实现

## 抽象基类
* 纯虚函数(pure) `=0`，含有纯虚函数的是抽象基类，其派生类必须给出该函数的定义否则也是抽象基类
* 不能创建一个抽象基类的对象
* 重构(refactoring)：重新设计类的体系以便将操作和/或数据从一个类移动到另一个类中

## 访问控制与继承
* `protected`同私有不可访问，同公有对于派生类与友元可访问，且派生类的成员或友元只能通过派生类对象来访问基类的受保护成员
* 派生访问说明符是为控制派生类用户对于基类成员的访问权限，`private`继承则全是私有
* 友元关系不能继承，派生类友元也不能随意访问基类成员
* 通过`using Base::size;`改变访问权限，当然也只能改变那些派生类可访问的名字
* 默认struct关键字公有继承，class关键字私有继承

## 继承中的类作用域
* 派生类的作用域嵌套在其基类的作用域之内
* 隐藏外层作用域的名字，通过作用域运算符来使用隐藏的成员
* 名字查找，如调用`p->mem()`
	* 先确定p的静态类型
	* 在p的静态类型对应的类中查找mem，如果找不到，则依次在直接类中不断查找直至到达继承链的顶端
	* 一旦找到mem，即进行常规的类型检查以确认调用是否合法
	* 假设调用合法，编译器将根据调用的是否是虚函数而产生不同的代码
		* mem是虚函数且我们通过引用或指针进行调用，则编译器产生的代码将在运行时确定到底运行该虚函数的哪个版本
		* 反之，如果mem不是虚函数，或我们通过对象进行的调用，则编译器产生一个常规函数的调用
* 派生类如果希望所有重载函数均对它可见，则要不覆盖全部，要不一个也不覆盖

## 构造函数与拷贝控制
* 先析构派生类再析构基类
* C++11派生类可重用其直接基类定义的构造函数，类不能继承默认、拷贝、移动构造函数；若派生类没有直接定义这些构造函数，则编译器将为派生类合成它们
* 用`using Disc_quote::Disc_quote;`实现构造函数的继承
* 如果子类的构造函数中没有显式调用父类的构造函数，则先调用父类的**默认构造函数**

## 容器与继承
* 当派生类对象被赋值给基类对象时，其中的派生类部分被切掉，因此容器和存在的继承关系的类型无法兼容
* 因此通常存放基类的指针
* 派生类应当反应与基类的“是一种is A”关系