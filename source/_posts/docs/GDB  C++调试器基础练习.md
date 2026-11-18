---
title: GDB C++调试器基础练习
date: 2026-11-11 14:11:28
tags:
  - C++
  - GDB
categories:
  - C++ 学习
  - GDB
published: true
---

## GDB / C++ 调试器基础练习

**练习环境：** Visual Studio 2026 / MSVC / Debug x64

**练习内容：**

1. **断点（Breakpoint）**：在 `add()` 函数中设置断点，按 F5 启动程序，程序成功暂停在指定代码位置。
    
2. **单步执行（Step Over）**：按 F10 执行当前语句，观察程序从 `add()` 返回 `main()` 的过程。
    
3. **变量查看（Watch）**：查看函数参数 `a = 2`、`b = 3`，计算结果为 `5`。
    
4. **调用栈（Call Stack）**：在 `add()` 断点处查看调用栈，确认调用关系为 `main() → add()`。
    
5. **继续执行（Continue）**：按 F5，程序继续运行并正常退出。
    

**练习结论：**

掌握了 C++ 调试器的基本使用，包括断点设置、单步执行、局部变量查看、调用栈分析及继续执行。理解了函数调用过程中程序执行位置和调用关系的变化。

**GDB 命令与 VS 调试器对应：**

- `break` → 设置断点
    
- `run` → 启动调试
    
- `next` → F10
    
- `print` → Watch / Locals
    
- `backtrace` → Call Stack
    
- `continue` → F5