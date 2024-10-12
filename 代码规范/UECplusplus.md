<!---
   Copyright (C) 2024  All rights reserved.

   Author        : OceanEyeFF
   Email         : fdch00@163.com
   File Name     : UECplusplus.md
   Last Modified : 2024-09-17 19:42
   Describe      : 

--->

UECplusplus
======

### 前言
UE引擎中使用的C++代码实际上是高度封装化的C++代码，所以在代码编写中我们需要同时遵循UE和C++的开发规则。
UE代码规则在虚幻本体的代码和变量命名规则上做拓展
Unreal代码指南: https://blog.csdn.net/ttod/article/details/134344302
Google代码指南: https://zhuanlan.zhihu.com/p/400788298

如果你熟悉C++但是不熟悉Unreal，我推荐你看这个csdn专栏 https://blog.csdn.net/ttod/category_12495998.html
如果不熟悉modern C++，我的建议是买本书

### 版权规范

公司名 + 版权
作者+联系方式+文件名+最后修改时间
网络上有很多一件生成更新版权和写作信息的插件，或者自己写一个也行

### 命名规则

#### 通用规则

* 明确代码的功能，或者概括代码的用途
* 文件名中避免使用和UE本体成员/文件高度相似的名称
* 多数时候使用帕斯卡命名法，多个单词组合时首字母大写

#### 文件命名

UnrealEngine内置了代码的头文件生成和文件生成，此处仅对文件的命名做出要求
1. 文件名需要提示阅读者代码的继承关系
2. 文件名长度不能超过32个字符(UE规则)
3. 文件名不能使用下划线
4. 如需使用hpp类型文件请与和头文件放在一个文件夹

#### 代码内命名

* 类型命名：参考UE命名规则
前缀：类名用一个额外的大写字母来区分变量名，例如:FSkin 是一个类名，Skin是FSkin的实例对象。
	* U：继承自UObject（虚幻引擎所有类的基类）
	* A：继承自AActor
	* S：继承自SWidget
	* I：抽象类
	* F：通用前缀
	* T：模板类
	* G：全局对象
	* E：枚举
	* e：枚举类中的子类
	* b：布尔类型
	* k：常量

* 变量命名：
	* 普通变量：TableName
	* 类数据成员: VariableName，结构体成员和普通变量一致
	* 常量：const kTableName k表示常量，使用驼峰命名法
	* 枚举命名：
	**   enum EErrorNumbers{
		eOK = 0,
		eErrorOutOfMemory,
		eErrorMemoryExceeded,
		eErrorRecursiveDepthExceed
	   }; **
	* 宏命名：MY_MACRO_THAT_SCARES_SMALL_CHILDREN
	* 函数：采用帕斯卡命名法: ValidateFuncName()
	* 蓝图函数：BPValidateFuncName()
	* 命名空间：遵循帕斯卡命名法，可以前后加下划线包裹如 __GameActorsFunctionColletion__

### 代码主体细则

#### 命名空间

* 将函数包裹在命名空间中，不要使用无意义的类和类静态成员
* 不要修改约定俗成的namespace，如:std

#### 类

* 构造函数不要使用虚函数（因为不会调），及时抛出异常，不要有失败初始化这种不确定操作
* 构造函数使用explicit 防止隐式类型转换
* 拷贝构造，赋值构造，移动构造，移动赋值。这些成套使用，要么都不用(可参考题图，使用DISALLOW宏)。
  当类型可能存在浅拷贝等情况时，尤其要注意
* 组合优于继承，has-a比is-a关系更灵活。可以使用override，final等关键字表示出继承方法，便于阅读。
  尽量不适用多继承，如果有看看还有没有其他方法。
* 对于纯方法的类，建议使用Interface作为后缀
* 只在合适的时候使用运算符重载，不要给通俗的运算符赋予让人意外的操作

#### 函数
* 函数参数顺序：输入参数在前，输入能用const就const，输出参数在后。引用参数最好都加上const，如有
  改变的可能，使用指针更直白
* 函数如果超过40行，就考虑能不能进一步分割
* 有类型后置的声明方法了：auto foo(int x) -> int;
* 函数默认参数和重载需要清晰，最好能做到看到调用点就知道是哪个函数
* 函数命名需要清晰的说明他的用途
	* 正面例子：**BPUpdateMaterial(UMaterial* MatInput)**

#### 变量、宏、函数顺序
	* 宏在头文件最顶端定义，注意代码之间宏有无冲突
	* 类内如有常量在写在最顶
	* 类内蓝图可访问变量在前，蓝图不可访问变量在后
	* 指针变量在前，非指针变量在后
	* 变量在函数前，同时善用定义先后顺序框定变量使用范围

#### 注释
* 应该始终遵守自注释代码风格，追求简洁易懂
* 所有的注释需要说明的问题：what and how and range
* 注释应该说明的（如果存在）：性能隐患、同步异步、线程安全等
* 文件开头要有版权注释（为了开源和负责）和文件说明
* 类注释：让读者知道这个类是什么、大致干了啥
* 短函数不要注释-代码体现的不用注释重复一遍
* 函数名称对作用说明很明显的不要重复注释
* TODO(someone): someone will do it
* 注释格式参考本文注释宏段落

#### Doxygen生成注释
* https://zhuanlan.zhihu.com/p/100223113
* https://cedar-renjun.github.io/2014/03/21/learn-doxygen-in-10-minutes/
* doxygen的注释规范要远比本文注释章节的规范更加严谨，有心者可以学习

#### 格式
* 一行80个字符 最长不超过120
* 正常缩进使用4个空格
* 函数返回类型和函数名在同一行，实在长，形参可以换行。参数直接换行用4个空格缩进。如果首个参数没
  换行，后续参数换了，和首个参数对齐
* 细节可以按照个人的爱好，如果团队有规范，优先遵循团队的规范

### 代码说明文档细则

# 所有的代码文件必须配备说明文档

文档首先考虑使用Markdown格式，其次考虑使用txt格式。
文档要由三个部分组成：
1. 说明这个代码的用途，继承/派生自什么，结构如何
2. 说明实现思路
3. 头文件内容及其各个函数的功能注释，引用文件和Pragma不需要附带

### 其他细节

#### 启用宏

* 中途输出调试宏
```c++
#define DEBUG_PRINT 1

#ifdef DEBUG_PRINT
        #define        debugPrint   Print
#else
        #define        debugPrint   //Print
#endif

template<class T> void Print( const T &x ) const;

```
