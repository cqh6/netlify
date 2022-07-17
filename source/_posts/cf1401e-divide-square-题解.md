---
title: CF1401E Divide Square 题解
date: 2022-07-17 17:21:52
updated: 2022-07-17 17:21:52
tags:
  - 树状数组
categories:
  - 题解
comments: true
---
### [题目链接](https://www.luogu.com.cn/problem/CF1401E)

根据平面图欧拉定理 ${\tt{点数}}-{\tt{边数}}+{\tt{面数}}={\tt{连通块数}}+1$

由于题中 **每条线的两个端点之一一定在正方形的边上**，所以连通块数为 $1$。

由于一条线段上的边数为这条线段上的点数 $-1$，可以得到 ${\tt{边数}}=2\times{\tt{点数}}-n-m-4$。

故 ${\tt{面数}}={\tt{点数}}-n-m-3$

考虑如何计算点数，从左往右扫，对于一条水平的线段，我们在扫到它左端点时标记一下它的所在的行可以产生交点，扫完后在取消标记。对于竖直的线段，我们只要在扫到它所在的位置的时候查询这个线段上有多少个被标记的点，即会产生多少个交点。

用树状数组维护即可。

### CODE

```cpp
#include <bits/stdc++.h>
#define int long long

using namespace std;

int read() {
	int x=0, f=0; char c=getchar();
	while (!isdigit(c)) f|=c=='-', c=getchar();
	while (isdigit(c)) x=(x<<3)+(x<<1)+(c^48), c=getchar();
	return f ? -x : x;
}

const int l=1, r=1e6+1;
int n, m, res, c[r+10];
vector<pair<int,int> > q[r+10], v[r+10];

void add(int x, int y) {
	while (x<=r) {
		c[x]+=y;
		x+=x&-x;
	}
}

int ask(int x) {
	int r=0;
	while (x) {
		r+=c[x];
		x-=x&-x;
	}
	return r;
}

signed main() {
	add(l, 1), add(r, 1);
	q[1].push_back({l, r});
	q[r].push_back({l, r});
	n=read(), m=read();
	for (int i=1; i<=n; i++) {
		int k=read()+1, x=read()+1, y=read()+1;
		q[k].push_back({x, y});
	}
	for (int i=1; i<=m; i++) {
		int k=read()+1, x=read()+1, y=read()+1;
		v[x].push_back({k, 1});
		v[y+1].push_back({k, -1});
	}
	for (int i=l; i<=r; i++) {
		for (auto [j, k]:v[i]) add(j, k);
		for (auto [j, k]:q[i]) res+=ask(k)-ask(j-1);
	}
	printf("%lld\n", res-n-m-3);
	return 0;
}
```