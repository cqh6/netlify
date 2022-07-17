---
title: CF1654G Snowy Mountain 题解
date: 2022-07-17 16:35:55
updated: 2022-07-17 16:35:55
tags:
  - 贪心
  - BFS
categories:
  - 题解
excerpt: []
comments: true
---
### [题目链接](https://www.luogu.com.cn/problem/CF1654G)

我们发现一种合法的解为走一段向下的路，在这中间有一些是水平的，但是我们发现可以来回在水平的上面行走，所以我们可以把所有水平的行走放到最后，所以我们只要求出高度最低的可以水平行走的一条边即可。

若起点的高度为 $h_u$，最低可以走到的水平边两点的高度为 $h_v$，则最多可以走 $2\times h_u-h_v$，因为可以走 $h_u$ 次下降和 $h_u$ 次水平，但因为没有比 $h_v$ 靠下的水平的可以走，所以可以走 $2\times h_u-h_v$ 步。

所以我们只要求出对于每个点可以到达最低的水平边的高度即可。

可以发现一个奇妙的性质：对于一个高度为 $x$ 的水平，他们下面要有 $2x$ 个点，所以所有水平边的高度之和在 $O(n)$，由此可得高度不同的水平最多有 $O(\sqrt n)$ 个。

所以我们可以对于每一个水平边的取值，用 01 BFS 计算出能到达它们的点就好了QwQ

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
int n, a[N], h[N], f[N], g[N];
vector<int> g1[N], g2[N];
queue<int> q;
deque<int> q0;

int main() {
	n=read();
	for (int i=1; i<=n; i++)
		if (read()) q.push(i), h[i]=1;
	for (int i=1; i<n; i++) {
		int x=read(), y=read();
		g1[x].push_back(y);
		g1[y].push_back(x);
	}
	while (!q.empty()) {
		int x=q.front(); q.pop();
		for (int y:g1[x])
			if (!h[y]) h[y]=h[x]+1, q.push(y);
	}
	for (int i=1; i<=n; i++) {
		bool ok=0;
		for (int j:g1[i])
			if (h[i]==h[j]) ok=1;
		if (ok) g2[h[i]].push_back(i);
	}
	memset(g, -1, sizeof g);
	for (int i=1; i<=n; i++) {
		if (g2[i].empty()) continue;
		memset(f, 0x3f, sizeof f);
		for (auto v:g2[i])
			f[v]=i+1, q0.push_back(v);
		while (!q0.empty()) {
			int u=q0.front(); q0.pop_front();
			for (int v:g1[u]) {
				if ((h[u]==h[v] || h[u]<h[v] && f[u]==h[u]) && f[v]>f[u]+1) f[v]=f[u]+1, q0.push_back(v);
				if (h[u]<h[v] && f[u]>h[u] && f[v]>f[u]) f[v]=f[u], q0.push_back(v);
			}
		}
		for (int u=1; u<=n; u++)
			if (f[u]==h[u] && g[u]==-1) g[u]=i;
	}
	for (int i=1; i<=n; i++)
		if (g[i]==-1) printf("%d ", h[i]-1);
		else printf("%d ", 2*(h[i]-1)-(g[i]-1));
	return 0;
}
```