# unityAndCsharp

- Scene (场景编辑 窗口)
- Game (游戏运行 窗口)
- Hierarchy (场景物体 列表窗口)
- Project (项目资源 列表窗口)
- Inspector (属性编辑 列表窗口)
- 其他常用调节窗口







# Csharp

- [简介](#简介)
- [CLS公共语言规范](#CLS公共语言规范)
- [.NET_Framework](#.NET_Framework)
- [通过命令行创建C#项目和编译运行](#通过命令行创建C#项目和编译运行)
- [C#基础语法](#C#基础语法)
    - [格式化字符串和格式化输出](#格式化字符串和格式化输出)
    - [数据类型转换](#数据类型转换)
    - 







## 简介

- `.NEW dotnet`
    - **Microsoft 新一代多语言开发平台, 用于构建和运行应用程序**
- `C# Csharp`
    - **Microsoft 专为 .NET 推出的高级编程语言**
- `Mono`
    - Novell公司支持在其他操作系统下开发.NET程序框架
    - Unity借助 Mono实现跨平台,  核心是 .NET framework 框架
- 



## CLS公共语言规范

> **源代码(.cs) -> CLS编译 -> CIL通用中间语言(.exe, .dll)   -> CLR编译(即时编译,公共语言运行库)  ->机器码**
>
>  CLS编译目的是 : 跨语言
>
> CLR编译目的是 : 优化, 跨平台



## .NET_Framework



## 通过命令行创建C#项目和编译运行

```bash
#MacOS: 先安装微软的C#安装包,才可以使用这些命令和开发C#应用.编译出来的是exe,可以借助Mono运行
#创建一个名为 console 的C#项目
$dotnet new console

#编译
$dotnet restore

#运行
$dotnet run
```



## C#基础语法

```c#
/*基础语法 */

using System;   /* 引用命名空间,和引用头文件相同 */

namespace Day01 /*定义命名空间, 里面包含了类, 主要对类进行逻辑上的划分,避免重名 */
{
    class Program /* 定义一个类 */
    {   
        static void Main(string[] args) /* 程序入口方法, 程序开始执行的地方 */
        {
            Console.WriteLine("Hello World!");  /* Console 是个工具类 */
         	Console.Title = "设置新的控制台标题";   /* 设置属性 */
            
            string input = Console.ReadLine(); /* 读取一行输入 */
            Console.WriteLine(input + "111");  /*字符串输出组合 */
            
            sbyte w_char;    //有符号 一字节 char
            byte  u_char;    //无符号 一字节 unsigned char
            char  char_2;    // 无符号字符, 2字节
            short c_short;
            ushort u_short;
            int c_int;
            uint u_int;
            long c_long;
            ulong u_long;
            
            float   folat_4;    // 4字节, 32位数据类型, 精度 7位 ,  1.5f
            double  double_8;   // 8字节, 64位数据类型, 精度 15~16位  1.5d
            decimal double_16;  // 16字节 128位数据类型,精度 28~29位(精度最高)  1.5m
                               //float 和 double 会出现舍入误差,但decimal不会,精度非常高
        }
    }
}

```



### 格式化字符串和格式化输出

```c#
using System;
namespace Str{
    class Program{
        static void Main(string[] args){
            /* 字符串格式化 */
            string str = string.Format("用尖括号占位符来进行输出{0}, {1}, {2}",1,2,3);

            /* 输出格式化 */
            Console.WriteLine("金额: {0:c}",10);  //以货币形式显示 ( 金额: ¥10.00)
            Console.WriteLine("{0:d2}", 1);   //整数宽度和0填充. (2位宽度, 显示 01 )
            Console.WriteLine("{0:f1}", 1.26); //浮点数小数点位数限制.会四舍五入.输出 1.3
            Console.WriteLine("{0:p1},{1:p}",0.1,0.1); //按照百分号格式化, 输出 10.0%,10%
        }
    }
}
```



### 数据类型转换

```c#
/* 将字符串转换为其他数据类型 */
string str="18";
int num01 = int.Parse(str);   // int.Parse 将字符串转换为 int
double num02 = double.Parse(str);    //将字符串转换为double. 括号内的数据必须 "像" 数据类型


/* 将任意数据数值类型 变换成字符串类型,来给字符串类型赋值 */
int num = 10;
string str = num.ToString();
```

