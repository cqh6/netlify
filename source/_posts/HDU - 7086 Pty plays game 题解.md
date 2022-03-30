---
index_img: https://img.xjh.me/random_img.php?type=bg&ctype=nature&return=302&seed=528321
title: HDU - 7086 Pty plays game 题解
date: 2021-11-09 10:58:00
updated: 2021-11-09 10:58:00
tags:
  - 方程
categories:
  - 题解
comments: true
---
## [题目](https://vjudge.net/problem/HDU-7086)

![](https://cdn5.maocdn.cn/img/2021/11/09/1e26abf1050bf6e1b.png)

## 题解

如果两边都只有一个士兵，属性分别为$h_1,d_1$和$h_2,d_2$，只需比较他们击败对面的时间即可

如果士兵$1$获胜，那么有：$\frac{h_2}{d_1}<\frac{h_1}{d_2}$，即$h_1d_1>h_2d_2$

并且观察得，在士兵1获胜后，他的血量变为了$h_1'=h_1-\frac{h_2d_2}{d_1}$，即$h_1'd_1=h_1d_1-h_2d_2$

因此定义一名士兵$x$的战斗力为$h_xd_x$，那么两名士兵战斗，战斗力高的会获胜，并且获胜后战斗力会降低对方战斗力

判断玩家和BOSS谁赢，只需判断两边队伍战斗力和的大小即可

而每一名的士兵血量和攻击力都是关于训练天数的一次函数，那么每边战斗力和为一个二次函数，即：

$\sum(h_i+dh_i\times x)(d_i+dd_i\times x)$

问题转化为了求最小非负整数使得二次函数值为正

我们考虑如何求在 $[0,1e9]$ 满足 $ax^2+bx+c\ge0$ 的最小值。

很显然如果有解答案只会在 $x=0$ 时或 $f(x)=0$ 时，因为有精度问题，所以只要在周围一小段区间判断一下就好了，代码也很清爽。

## 代码

```cpp
#include <bits/stdc++.h>
#define int __int128

using namespace std;

int a, b, c, res;

int read() {
	int x=0, f=0; char c=getchar();
	while (!isdigit(c)) f|=c=='-', c=getchar();
	while (isdigit(c)) x=x*10+(c^48), c=getchar();
	return f ? -x : x;
}

void comp(int x) {
	if (x>=0 && x<=1e18 && (a*x*x+b*x+c)>0) res=res==-1 ? x : min(res, x);
}

signed main() {
	int T=read();
	while (T--) {
		int n=read(), m=read(); a=0, b=0, c=0, res=-1;
		for (int i=1; i<=n; i++) {
			int h=read(), dh=read(), d=read(), dd=read();
			a+=dh*dd;
			b+=h*dd+d*dh;
			c+=d*h;
		}
		for (int i=1; i<=m; i++) {
			int h=read(), dh=read(), d=read(), dd=read();
			a-=dh*dd;
			b-=h*dd+d*dh;
			c-=d*h;
		}
		comp(0);
		if (a==0 && b!=0) { int x=-c/b; comp(x-1), comp(x), comp(x+1); }
		else if (a!=0) {
			int d=b*b-4*a*c, x;
			x=(-1.0*b+sqrt(1.0*d))/a/2; comp(x-1), comp(x), comp(x+1);
			x=(-1.0*b-sqrt(1.0*d))/a/2; comp(x-1), comp(x), comp(x+1);
		}
		if (res==-1) puts("none");
		else printf("%lld\n", (long long)res);
	}
	return 0;
}
```