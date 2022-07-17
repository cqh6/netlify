---
title: CF1190C Tokitsukaze and Duel 题解
date: 2022-07-17 16:12:57
updated: 2022-07-17 16:12:57
tags:
  - 博弈
categories:
  - 题解
comments: true
---
### [Link](https://www.luogu.com.cn/problem/CF1190C)

有几个显然的性质：

- 如果先手第一步无法获胜，那么后手可以通过模仿保证自己不会输。
- 如果后手第一次操作后无法获胜，那么先手也可以保证自己不会输掉。

所以可以发现如果先手要赢必须第一步直接赢，后手要赢必须保证无论先手第一步怎么操作都可以获胜。否则两人平局。

先手一步是否能赢很好判断，只要枚举每一个区间算出剩下的区间是否是同一个数即可（可用前缀和或后缀和算出区间和判断是否为同一个数）

后手也很好判断，在先手不能必胜的条件下，枚举先手第一步走哪里，判断剩下的区间

### CODE

```cpp
#include <bits/stdc++.h>

using namespace std;

int read() {
	int x=0, f=0; char c=getchar();
	while (!isdigit(c)) f|=c=='-', c=getchar();
	while (isdigit(c)) x=(x<<3)+(x<<1)+(c^48), c=getchar();
	return f ? -x : x;
}

const int N=1e5+10;
int n, k, l[N], r[N];
char s[N];

void init() {
	l[1]=1, r[n]=n;
	for (int i=2; i<=n; i++) l[i]=s[i]==s[i-1]?l[i-1]:i;
	for (int i=n-1; i; i--) r[i]=s[i]==s[i+1]?r[i+1]:i;
}

bool chk1() {
	if (n==k || l[n-k]==1 || r[k+1]==n) return 1;
	for (int i=2; i<=n-k; i++)
		if (s[i-1]==s[i+k] && l[i-1]==1 && r[i+k]==n) return 1;
	return 0;
}

bool chk2() {
	if (k*2<n) return 0;
	for (int i=2; i<=n-k; i++)
		if (l[i-1]!=1 || r[i+k]!=n) return 0;
	return 1;
}

int main() {
	n=read(), k=read();
	scanf("%s", s+1);
	init();
	if (chk1()) puts("tokitsukaze");
	else if (chk2()) puts("quailty");
	else puts("once again");
	return 0;
}
```