---
title: CF1635F Closest Pair 题解
date: 2022-07-17 16:34:39
updated: 2022-07-17 16:34:39
tags:
  - 树状数组
categories:
  - 题解
comments: true
---
### [题目链接](https://www.luogu.com.cn/problem/CF1635F)

可以发现一个奇妙的结论（反正我是想不到的）：

令

![](/images/uploads/1.png)

则答案一定为 $(L_i,i),(i,R_i)$ 中的一个

 反证：

设最优决策为 $(i, j)$

- $w_i\le w_j$，若 $i\ne L_j$，则 $i<L_j<j,|x_i-x_{L_j}|<|x_i-x_j|,w_i+w_{L_j}\le w_i+w_j$，则 $(i,L_j)$ 更优。
- $w_i>w_j$ 同理

所以的对于每一个 $i$ 都只有 $(L_i,i),(i,R_i)$ 这 $2$ 个区间，总共只有 $2n$ 个区间可能是答案。

所以我们先用单调栈预处理出 $L_i$ 和 $R_i$，然后询问离线，树状数组维护，类似二维数点就好了！

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

const int N=3e5+10;
int n, q, p[N], w[N], L[N], R[N], ans[N], c[N];
vector<pair<int, int> > can[N], que[N];
stack<int> s;

int calc(int x, int y) {
	return (p[y]-p[x])*(w[x]+w[y]);
}

void add(int x, int y) {
	while (x<=n) {
		c[x]=min(c[x], y);
		x+=x&-x;
	}
}

int query(int x) {
	int res=5e18;
	while (x) {
		res=min(res, c[x]);
		x-=x&-x;
	}
	return res;
}

signed main() {
	n=read(), q=read();
	for (int i=1; i<=n; i++) p[i]=read(), w[i]=read();
	s.push(0);
	for (int i=1; i<=n; i++) {
		while (s.top() && w[s.top()]>w[i]) s.pop();
		L[i]=s.top(); s.push(i);
	}
	while (!s.empty()) s.pop(); s.push(0);
	for (int i=n; i>=1; i--) {
		while (s.top() && w[s.top()]>w[i]) s.pop();
		R[i]=s.top(); s.push(i);
	}
	for (int i=1; i<=n; i++) if (L[i]) can[L[i]].push_back({i, calc(L[i], i)});
	for (int i=1; i<=n; i++) if (R[i]) can[i].push_back({R[i], calc(i, R[i])});
	for (int i=1; i<=q; i++) {
		int x=read(), y=read();
		que[x].push_back({y, i});
	}
	for (int i=1; i<=n; i++) c[i]=5e18;
	for (int i=n; i>=1; i--) {
		for (auto j:can[i]) add(j.first, j.second);
		for (auto j:que[i]) ans[j.second]=query(j.first);
	}
	for (int i=1; i<=q; i++) printf("%lld\n", ans[i]);
	return 0;
}
```