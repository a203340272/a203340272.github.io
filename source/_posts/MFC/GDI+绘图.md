---
title: GDI+绘图
date: 2026-02-06
tags:
  - cpp
  - MFC
categories:
  - MFC
  - 学习
publish: false
---

==**Qt**==是跨平台的应用程序框架，可以在多个平台上运行，而==**GDI+**==只能运行在
Windows 操作系统上。
Q**t**绘图是基于QPainter对象进行绘图，而**GDI+**绘图是基于Graphics对象进行绘图。
Q**t**提供了一套用于GUI编程的类库，包括了绘图、网络、XML、数据库、多线程等模块，而**GDI+**只提供了一套用于绘制图形的API。]{.mark}

<https://blog.csdn.net/devilhy1986/article/details/39576623>

==GdiplusBrush==是 GDI+ 中用于绘制图形的类，它位于 **GDI+**
库中，而不是传统的 GDI（==CBrush==）。在 MFC 中，使用 GDI+
需要包含特定的头文件。

**需要包含的头文件：**
```cpp
#include \<gdiplus.h\>
```
这个头文件是 GDI+ 的主要头文件，它包含了大部分 GDI+ 类的定义，包括
==Gdiplus::Brush==（==GdiplusBrush==），==Gdiplus::Graphics==
等。

**如何使用 GDI+ 的 ==Brush==

在 MFC 中使用 GDI+ 时，你需要初始化
GDI+，通常是在应用程序启动时。以下是一个简单的例子，展示了如何在 MFC
中使用]{.mark}

GdiplusBrush：
```cpp

#include \<afxwin.h\>

#include \<gdiplus.h\>

#pragma comment(lib, \"gdiplus.lib\")

using namespace Gdiplus;

class CMyView : public CView {

protected:

void OnDraw(CDC\* pDC) override {

Graphics graphics(pDC-\>m_hDC); // 获取图形对象

SolidBrush brush(Color(255, 0, 255, 0)); // 创建绿色画刷

// 使用 GDI+ 绘制矩形

graphics.FillRectangle(&brush, 50, 50, 200, 200);

}

};

class CMyApp : public CWinApp {

public:

BOOL InitInstance() override {

GdiplusStartupInput gdiplusStartupInput;

ULONG_PTR gdiplusToken;

GdiplusStartup(&gdiplusToken, &gdiplusStartupInput, NULL); // 初始化
GDI+

CWinApp::InitInstance();

return TRUE;

}

int ExitInstance() override {

GdiplusShutdown(gdiplusToken); // 关闭 GDI+

return CWinApp::ExitInstance();

}

};

CMyApp theApp;
```
**关键点说明：**

1. #include \<gdiplus.h\>：这是包含 GDI+ 所需的头文件。

2. Gdiplus::Graphics：在 MFC 中 GDI+时，必须获取一个 ==Graphics==对象，它是 GDI+ 绘图的核心对象。

3. SolidBrush：用于创建填充颜色的画刷。你可以创建不同类型的画刷，如 ==SolidBrush==（实心颜色画刷）、==TextureBrush==（纹理画刷）等。

4. GdiplusStartup和 ==GdiplusShutdown==**：在应用程序的初始化和退出时，你需要启动和关闭GDI+，分别调用 ==GdiplusStartup== 和 ==GdiplusShutdown==。

**总结**

- GdiplusBrush 这个名称是 GDI+ 中的一个类，用来描述画刷。MFC 中使用 GDI+时，您应该包含 ==gdiplus.h== 头文件，并初始化 GDI+。

- GDI+ 提供了比传统 GDI更强大的图形绘制功能，适用于需要更复杂图形操作的应用。
