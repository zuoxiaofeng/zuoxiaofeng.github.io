---
title: C/C++ 静态库介绍
---

# 什么是静态库
>C/C++程序编译一般需经预处理、编译、汇编和链接这四个个步骤，静态库从文件的内容上来看就是C/C++源代码经预处理、编译、汇编这三个步骤生成的二进制文件，当提供给他人使用的时候还要提供声明函数或类的头文件。
## 一点结论
>当其他程序依赖一个静态库，在这个程序编译的预处理、编译、汇编这三个步骤中只是会用到静态库头文件中的声明并不会使用到静态库的二进制文件，只有在链接的时候才会去链接静态库的二进制文件。所以可以得出以下结论：
* 只有当一个程序编译时有链接这一步，才会需要提供依赖的静态库的二进制文件   
>静态库不需要执行链接这一步，所以编译一个静态库的时候是不需要提供它依赖的其他静态库的二进制文件。
>> 注意：Linux下GCC在编译动态库（.so）在链接时当依赖的库不存在的时候默认是不报错的，不过有参数选项可以控制这个行为，这一点会在C/C++动态库介绍中详细介绍  

# 为什么要有静态库？
>要回答这个问题我们首先要知道什么是静态库， 所以在文章开头我介绍了什么是静态库
>基于文章开头的介绍可以得出静态库的以下优点：
* 隐藏代码实现
>>静态库是C/C++代码经过预处理、编译、汇编生成的二进制文件，在提供给别人调用的同时还可以隐藏代码实，虽然有反汇编技术但真想要还原成可读性好的C/C++代码很难。一句题外话现在都提倡拥抱开源这一点不知道该算缺点还是优点。
* 减少重复编译
>>相比直接使用静态库的源代码，在使用静态库的时候减少了对静态库源码的预处理、编译、汇编。可以想象如果没有静态库我们在编译C/C++程序的时候每次都要完全编译用到的标准库源代码是件多么可怕的事
* 简化了使用外部代码功能
>>如果直接引用别人的源码而不是静态库/动态库，我们就需要将别人的代码引入到我们的工程中来。可以想象对于动辄成千上百个源文件的开源库，对于千差万别的构建方式，想把别人的代码引入到我么的工程中来是多么繁琐的一件事（不过实际项目中还真有人这么做了，一个例子就是chromium内核代码工程，这个工程里面几乎将所有用到的第三方库的代码都统一改为用GYP这个构建系统来编译， 这里顺便安利一下GYP，GN和Ninja这三个构建工具组成的构建系统）。

# 静态库编译以及使用
说了那么多终于该上代码了,代码很简单  
* 静态库代码  
static-library-example1.h
```
#ifndef __ZXF_STATIC_LIBRARY_EXAMPLE_HEADER__
#define __ZXF_STATIC_LIBRARY_EXAMPLE_HEADER__

int add(int a, int b);

#endif
```
static-library-example1.cpp
```
#include "static-library-example1.h"
int add(int a, int b)
{
	return a + b;
}
```
* 测试程序代码  
example1-main.cpp
```
#include "static-library-example1.h"
#include <iostream>

int main(int argc, char const* argv[])
{
    std::cout << "10 + 20 = " << add(10, 20) << std::endl;
    return 0;
}
```

## Windows平台编译以及使用

* 静态库编译
```
1.编译cpp为obj
cl.exe /nologo /TP /DWIN32 /D_WINDOWS /W3 /GR /EHsc /MDd /Zi /Ob0 /Od /RTC1 /FS -c static-library-example1.cpp /Fostatic-library-example1.cpp.obj
2.链接obj为lib
link.exe /lib /nologo /machine:x64 static-library-example1.cpp.obj /out:static-library-example1.lib
```
* 测试程序编译
```
cl.exe /nologo /TP /DWIN32 /D_WINDOWS /W3 /GR /EHsc /MDd /Zi /Ob0 /Od /RTC1 /FdTARGET_COMPILE_PDB /FS -c example1-main.cpp
```

***未完待续***
***2020/10/28 今天开始继续***

## Linux平台编译以及使用

# 依赖关系处理
## 静态库依赖静态库
## 静态库依赖动态库

# 总结