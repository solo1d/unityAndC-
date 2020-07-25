## 目录

- [标准C#插件代码](#标准C#插件代码)
- [c++插件代码模版和命令](#c++插件代码模版和命令)
- [将插件.dylib或者.bundle放入unity](#将插件.dylib或者.bundle放入unity)
- [C++和C#互相调用的简单代码](#C++和C#互相调用的简单代码)



**Csharp的`byte`是无符号单字符类型(1字节), C++是有符号的, C#的`sbyte`是有符号的char**

**C++中的 `void*` 指针 在C#中使用 `IntPtr` 代替**

- MacOS用 Xcode生成 `.bundle` **动态库**  文件使用, 也就是 MACos-> bundle 项目. 选择release 项目生成, 不要用 debug
- IOS 使用 Xcode 生成  `.a` **静态库**  , 也就是 IOS -> static Library 项目, 也选择 release 项目生成, 还要将生成 **build only device 选择到 Generic IOS Device**,(左上角那里显示).





## 标准C#插件代码



```c#
using UnityEngine;

//该命名空间必须包含
using System.Runtime.InteropServices; 

class SomeScript : MonoBehaviour {

    #if UNITY_IPHONE
        //在iOS上，插件静态链接到可执行文件，因此我们必须使用__Internal作为库名称。
        [DllImport ("__Internal")]
    #else
        //其他平台会动态加载插件，因此传递名称插件的动态库。
        [DllImport ("PluginName")]
    #endif
        
        // 下面这是一个简单的方法
		private static  float FooPluginFunction () { return 5.0F; } 
        void Awake () {
        // 在插件内调用FooPluginFunction并将5打印到控制台
       	 	print (FooPluginFunction ());
	    }
}
```





## c++插件代码模版和命令



C#代码引用动态库文件  Mylibrary 在不同平台下对应的文件为 
Windows————->MyLibrary.dll; 
Linux—————–>libMyLibrary.so; 
OSX——————>libMyLibrary.dylib.  (有时引用需要变更为 libMyLibrary)

```c++
// C++动态库插件代码, 可以生成动态库交给unity中的 C#来引用和使用.

// 编译命令 g++ -std=c++14 -shared -fpic -o CppNet100.dylib CppNet100.cpp

// CppNet100.cpp
#ifndef  __CPP_NET_100_DLL_H__
#define  __CPP_NET_100_DLL_H__

//注意要添加extern “C”，以说明函数是C风格的外部函数，可供动态调用
extern "C" {
#if _WIN32
        /* 这是win下动态库代码, _declspec(dllexport) 这个表示是动态库 */
        _declspec(dllexport) int  Add(int a, int b) {
                return a + b;
        }
#else
        int Add(int a, int b){
                return a + b;
        }
#endif
}
#endif
```



## 将插件.dylib或者.bundle放入unity

> **默认插件.dylib放入的位置:**
>
> | 位置,相对目录                               | 作用                                                         |
> | ------------------------------------------- | ------------------------------------------------------------ |
> | **Assets/../Editor/x86_64**                 | 将设置插件仅与编辑器兼容，并根据子文件夹分配CPU值。          |
> | **Assets/../Editor**                        | 插件将设置为仅与编辑器兼容，并且在构建到平台时将不会使用。   |
> | **Assets/../Plugins/x86_64**                | x64和x86独立插件将设置为兼容                                 |
> | **Assets/Plugins/iOS**                      | 插件将设置为仅与iOS兼容                                      |
> | **Assets/Plugins/WSA/**(**x86** or **ARM**) | 插件将设置为仅与 **通用Windows平台**，如果子文件夹中有CPU，则也会设置CPU值。可以使用Metro关键字代替WSA。 |
> | **Assets/Plugins/PS4**                      | 插件将设置为仅与 **Playstation 4**                           |

**如果要删除动态库,那么需要在手动删除文件之后,重启unity**





## C++和C#互相调用的简单代码

C#

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

/* 引用 unity的 命名空间 */
using System.Runtime.InteropServices;

/* 与动态库进行回调函数所使用的命名空间 */
using System;
using AOT;


public class NewBehaviourScript : MonoBehaviour
{
    // C#的回调函数是代理模式 delegate 代理模式关键字
    // 该函数就是 代理类型, 交给C++ 去使用, C++应该设置成一个函数指针
    // c++ 对应的函数指针代码 typedef void (*CallBakc1) (const char* s);
    public delegate void CallBakc1(string s);  // C++设置这个


    [MonoPInvokeCallback(typeof(CallBakc1))]   /* 设置该函数 代理 CallBakc1 函数 */
    public  void CallSackFun1(string s) // C#调用这个
    {
        print(s);
    }

    //其他平台会动态加载插件，因此传递名称插件的动态库。
    // CppNet100.dylib
    // 使用导入的动态库的函数.CppNet100.dylib
    // 如果是.bundle 格式插件(xcode生成) ,也这么写
    // C#的string 对应 C++的是 const char*
#if (UNITY_IPHONE || UNITY_WEBGL) && !UNITY_EDITOR  // 为了兼容IPhone
    [DllImpotr("__Internal")]
#else
    [DllImport("CppNet100")]
#endif
    public static extern int Add(int a, int b);   
    
    
#if (UNITY_IPHONE || UNITY_WEBGL) && !UNITY_EDITOR
    [DllImpotr("__Internal")]
#else
    [DllImport("CppNet100")]
#endif
    public static extern void TestCall1(string str1, CallBakc1 cb);


    // Start is called before the first frame update
    void Start()
    {
        print(Add(1, 2));
        TestCall1("Hei Hei", CallSackFun1);
    }

    // Update is called once per frame
    void Update()
    {
        
    }
}
```

c++

```c++
#ifndef  __CPP_NET_100_DLL_H__
#define  __CPP_NET_100_DLL_H__

#ifdef _WIN32
	/* Win 需要这个宏来定义 可调用动态库函数  _declspec(dllexport) */
	#define EXPORT_DLL  _declspec(dllexport)
#else
	#define EXPORT_DLL
#endif

#include  <string>
extern "C" {
	EXPORT_DLL int  Add(int a, int b){
		return a + b;
	}

	// 声明一个函数指针.  C#的string 对应 C++的是 const char*
	typedef void (*CallBakc1) (const char* str);
	
    /* 需求一个函数指针 */
	EXPORT_DLL void  TestCall1(const char* str1, CallBakc1 cb){  
		std::string s = "Hello ";
		s += str1;
		cb(s.c_str()); //回调函数
    }
}
#endif

```



