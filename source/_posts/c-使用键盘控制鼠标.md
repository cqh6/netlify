---
title: C++ 使用键盘控制鼠标
date: 2022-05-20 19:46:37
updated: 2022-05-20 19:46:37
categories:
  - 其他
comments: true
---
闲着没事乱写的东西。

有时候按鼠标声音比较响，影响别人学习，于是就乱写了这个，当按下 `~` 按键时会按下鼠标，松开后自动取消按下鼠标。

当然也可以根据自己的需求修改按键qwq

### CODE

```cpp
#include <bits/stdc++.h> 
#include <windows.h>
#define KEY_DOWN(VK_NONAME) ((GetAsyncKeyState(VK_NONAME) & 0x8000) ? 1:0)
using namespace std;
int main() {
    while (1) {
        while (!KEY_DOWN(192));
        mouse_event(MOUSEEVENTF_LEFTDOWN,0,0,0,0);
        while (KEY_DOWN(192));
        mouse_event(MOUSEEVENTF_LEFTUP,0,0,0,0);
        Sleep(100);
    }
    return 0;
}
```