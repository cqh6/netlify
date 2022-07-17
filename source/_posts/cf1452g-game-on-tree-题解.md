---
title: CF1452G Game On Tree 题解
date: 2022-07-17 16:32:24
updated: 2022-07-17 16:32:24
tags:
  - 贪心
  - BFS
categories:
  - 题解
comments: true
---
### [题目链接](https://www.luogu.com.cn/problem/CF1452G)

### 题意

$a$ 被 $b$ 的好多点在树上追（每回合 $a$ 先动，然后 $b$ 的所有点都单独动），$b$ 的点固定，问每一个 $a$ 的位置，$a$ 最多能保持多久不被追。

$n\leq 10^{5}$

由于 $b$ 是不变的，我们记 $dis(x)$ 表示 $x$ 距 $b$ 距离最小的一个点的距离。

可以用 BFS 预处理出，然后我们从 $dis(x)$ 从大到小做，发现每个点只会走向 $dis$ 更大的点，然后 DFS 往 $dis$ 大的方向走就好了，时间复杂度：$O(\tt{跑得过})$

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

const int N=2e5+10;
int n, k, mx, d[N], ans[N], now[N];
vector<int> v[N], ds[N];
queue<int> q;

void dfs(int x, int fa, int w, int z) {
	if (now[x]>=w) return;
	now[x]=w;
	if (!ans[x]) ans[x]=z;
	for (int y:v[x])
		if (y!=fa) dfs(y, x, w-1, z);
}

int main() {
	n=read();
	for (int i=1; i<n; i++) {
		int x=read(), y=read();
		v[x].push_back(y);
		v[y].push_back(x);
	}
	k=read(); memset(d, -1, sizeof d);
	for (int i=1; i<=k; i++) {
		int x=read();
		if (d[x]==-1) q.push(x), d[x]=0;
	}
	while (!q.empty()) {
		int x=q.front(); q.pop();
		for (int y:v[x]) {
			if(d[y]!=-1) continue;
			d[y]=d[x]+1, q.push(y);
			ds[d[y]].push_back(y);
			mx=max(mx, d[y]);
		}
	}
	for (int i=mx; i>=1; i--)
		for (int x:ds[i]) dfs(x, 0, i, i);
	for (int i=1; i<=n; i++) printf("%d ", ans[i]);
	return 0;
}
```