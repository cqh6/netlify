---
title: C++ 鼠标连点器
date: 2022-03-26 13:38:00
updated: 2022-03-26 13:38:00
tags:
  - 分享
categories:
  - 其他
comments: true
---

闲着没事写的，玩猫国建设者的时候可以用，不过会判断作弊qwq

```cpp
#include<bits/stdc++.h> 
#include<windows.h>
using namespace std;
int x;
int main()
{
    system("mode con cols=40 lines=15");
    SetWindowPos(GetConsoleWindow(),HWND_TOPMOST,0,0,0,0,SWP_NOMOVE|SWP_NOSIZE);
    cout<<"How many clicks per second? ";
    scanf("%d",&x);
    x=1000/max(x,1);
    cout<<"Press F8 to start or end,press F7 to change the speed.\n";
    while(1)
    {
        if(GetAsyncKeyState(VK_F8))
        {
            printf("start\n");
            Sleep(500);
            while(1)
            {
                mouse_event(MOUSEEVENTF_LEFTDOWN|MOUSEEVENTF_LEFTUP,0,0,0,0);
                Sleep(x);
                if(GetAsyncKeyState(VK_F7))
                {
                    cout<<"end\n";
                    break;
                }
                if(GetAsyncKeyState(VK_F8))
                {
                    cout<<"end\n";
                    Sleep(500);
                    break;
                }
            }
        }
        if(GetAsyncKeyState(VK_F7))
        {
            cout<<"How many clicks per second? ";
            cin>>x;
            x=1000/max(x,1);
        }
    }
    return 0;
}
```
