---
title: MFC DLL（动态链接库）
date: 2026-02-06
tags:
  - cpp
  - MFC
categories:
  - MFC
  - 学习
publish: false
---

MFC 中，DLL（动态链接库）的初始化通常涉及到几个关键步骤，包括 DLL
的入口点、全局的初始化、以及FC 的初始化。下面是一个简单的 MFC DLL
初始化例程的示例，展示如何设置和初始化 MFC DLL。

**MFC DLL 的基本结构**

创建 MFC DLL 项目在 Visual Studio 中选择创建 MFC DLL 项目。

\*\* DLL 的入口点\*\*：DLL 的入口点是 ==DllMain== 函数。

初始化 MFC：在 DLL 的初始化过程中，调用 MFC 的初始化函数。

**示例代码**

以下是一个简单的 MFC DLL 初始化例程：

**1. DLLMain 函数**
```cpp


#include \"pch.h\" // 预编译头文件

#include \"framework.h\" // MFC 框架

#include \"YourDllName.h\" // DLL 的头文件

// DLL 的全局变量

HINSTANCE g_hInstance = NULL;

// DLL 的入口点

BOOL APIENTRY DllMain(HINSTANCE hinstDLL, DWORD fdwReason, LPVOID
lpReserved) {

switch (fdwReason) {

case DLL_PROCESS_ATTACH:

// 当进程加载 DLL 时

g_hInstance =instDLL;

// 初始化 MFC

if (!AfxWinInit(hinstDLL, NULL, ::GetCommandLine(), 0)) {

return FALSE; // 初始化失败

}

break;

case DLL_THREAD_ATTACH:

// 当线程被创建时

break;

case DLL_THREAD_DETACH:

// 当线程被销毁时

break;

case DLL_PROCESS_DETACH:

// 当进程卸载 DLL 时

break;

}

return TRUE; // 成功

}
```
**2. 导出函数**

在 DLL
中，你可能需要导出一些函数供其他应用程序调用。以下是一个简单的导出函数示例：
```cpp

extern \"C\" \_\_declspec(dllexport) void MyFunction() {

AfxMessageBox(\_T(\"Hello from MFC DLL!\"));

}
```
**3. 头文件（YourDllName.h）**

确保在头文件中使用 ==__declspec(dllexport)== 来标记要导出的函数：
```cpp


#pragma once

#ifdef YOURDLL_EXPORTS

#define YOURDLL_API \_\_declspec(dllexport)

#else

#define YOURDLL_API \_\_declspec(dllimport)

#endif

extern \"C\" YOURDLL_API void MyFunction();
```

**4. 项目设置**

在项目属性中，确保选择了正确的设置：

C/C++ -\> **预处理器** -\> **预处理器定义** 中添加 ==YOURDLL_EXPORTS==。

链接器 -\> **系统** -\> **子系统** 设置为 ==动态链接库==。

**5. 使用 DLL**

在一个 MFC 应用程序中使用这个 DLL 时，你需要加载 DLL 并调用导出的函数：
```cpp


#include \<windows.h\>

#include \"YourDllName.h\"

void CallMyFunction() {

HINSTANCE hDll = LoadLibrary(\_T(\"YourDllName.dll\"));

if (hDll != NULL) {

// 获取函数地址

typedef void (\*MYFUNCTION)();

MYFUNCTION pMyFunction = (MYFUNCTION)GetProcAddress(hDll,
\"MyFunction\");

if (pMyFunction) {

pMyFunction(); // 调用函数

}

FreeLibrary(hDll); // 卸载 DLL

}

}
```
**总结**

以上是一个简单的 MFC DLL 初始化例程。关键步骤包括设置 DLL
的入口点、初始化
MFC、导出函数等。根据需要，你可以扩展这个基础结构，添加更多的功能和复杂性。如果你有特定的需求或问题，请告诉我，我可以提供更详细的帮助。
