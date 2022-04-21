---
title: C++ 鼠标录制
date: 2022-04-21 11:03:45
updated: 2022-04-21 11:03:45
tags:
  - 分享
categories:
  - 其他
keywords: []
excerpt: []
comments: true
---
闲着 闲着没事写的 C++ 鼠标录制。

按 F7 修改，依次点击需要点击的即可。

按 F8 后将会自动按原来的顺序不停点击。

又臭又长又废的代码：

```cpp
#include <bits/stdc++.h>
#include <windows.h>
#define KEY_DOWN(VK_NONAME) ((GetAsyncKeyState(VK_NONAME) & 0x8000) ? 1:0)
using namespace std;
vector<POINT>v;
int main() {
    system("mode con cols=40 lines=15");
    while (1) {
        if(GetAsyncKeyState(VK_F8)) {
            while (GetAsyncKeyState(VK_F8));
            puts("start");
            while (!GetAsyncKeyState(VK_F8)) {
                for (auto t:v) {
                    SetCursorPos(t.x, t.y);
                    mouse_event(MOUSEEVENTF_LEFTDOWN|MOUSEEVENTF_LEFTUP,0,0,0,0);
                    Sleep(50);
                }
            }
            while (GetAsyncKeyState(VK_F8));
            puts("end");
        }
        if(GetAsyncKeyState(VK_F7)) {
            while (GetAsyncKeyState(VK_F7));
            puts("change-start");
            v.clear();
            while (!GetAsyncKeyState(VK_F7)) {
                if (KEY_DOWN(VK_LBUTTON)) {
                    POINT p;
                    GetCursorPos(&p);
                    v.push_back(p);
                    while (KEY_DOWN(VK_LBUTTON));
                }
            }
            puts("change-end");
            while (GetAsyncKeyState(VK_F7));
        }
    }
    return 0;
}
``废物