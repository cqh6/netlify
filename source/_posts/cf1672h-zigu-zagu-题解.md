---
title: CF1672H Zigu Zagu 题解
date: 2022-07-17 16:33:37
updated: 2022-07-17 16:33:37
tags:
  - 贪心
categories:
  - 题解
comments: true
---
### [题目链接](https://www.luogu.com.cn/problem/CF1672H)

好牛马的题根本不配 $2700$

令一个区间内形如 $00$ 的数量为 $x$，形如 $11$ 的数量为 $y$。

由于每次删除的都是极长的：

- 如果删除了一个区间长度为偶数，则 $x$ 和 $y$ 都减一。

- 如果长度为奇数，则 $x$ 和 $y$ 当中的一个减一。

若 $x,y$ 都不为 $0$，就一定存在一种方案删除掉一段偶数，否则一定存在一种方案删除掉长度为奇数的。

所以一共最少删除 $\max(x,y)$ 次就可以使 $x=0,y=0$，只需在删一次就可以删完整个区间。

所以 $ans=\max(x,y)+1$。

预处理维护前缀和就好了QwQ

### CODE

```cpp
#include <bits/stdc++.h>

using namespace std;

int read() {
	int x=0, f=0; char c=getchar();
	while (!isdigit(c)) f|=c=='-',  c=getchar();
	while (isdigit(c)) x=(x<<3)+(x<<1)+(c^48), c=getchar();
	return f ? -x : x;
}

const int N=2e5+10;
int n, q, s0[N], s1[N];
char s[N];

int main() {
	n=read(), q=read();
	scanf("%s", s+1);
	for (int i=2; i<=n; i++) {
		s0[i]=s0[i-1]+(s[i]=='0' && s[i-1]=='0');
		s1[i]=s1[i-1]+(s[i]=='1' && s[i-1]=='1');
	}
	while (q--) {
		int l=read(), r=read();
		int x=s0[r]-s0[l], y=s1[r]-s1[l];
		printf("%d\n", max(x, y)+1);
	}
	return 0;
}
```