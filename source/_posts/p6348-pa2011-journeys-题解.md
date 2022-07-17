---
title: P6348 [PA2011]Journeys 题解
date: 2022-07-17 16:36:51
updated: 2022-07-17 16:36:51
tags:
  - 线段树
  - 最短路
categories:
  - 题解
comments: true
---
### [题目链接](https://www.luogu.com.cn/problem/P6348)

我家门前有两棵树，一棵是入树，一棵是出树（狗头，入树的边从上往下连，出树的边从下往上连。

对于一个区间连边，建 $2$ 个辅助节点，之间连一条权值为 $1$ 的边。从出树的 $O(\log n)$ 个节点向一个辅助点连一条权值为 $0$ 的边，再从另一个虚拟节点向入树连一条权值为 $0$ 的边，跑 01BFS 就好了。

注意：两棵树的同一个节点间要连一条权值为 $0$ 的边，以下代码没连，是因为它们是同一个节点了QwQ

### CODE

```cpp
#include <bits/stdc++.h>
#include <iterator>

using namespace std;

int read() {
	int x=0, f=0; char c=getchar();
	while (!isdigit(c)) f|=c=='-', c=getchar();
	while (isdigit(c)) x=(x<<3)+(x<<1)+(c^48), c=getchar();
	return f ? -x : x;
}

const int N=4e6+10;
int n, q, S, tot, ls[N], rs[N], dis[N], h[N], cnt;
struct Edge{
	int to, next, value;
} e[N<<1];

void insert(int x, int y, int z) {
	e[++cnt]={y, h[x], z};
	h[x]=cnt;
}

void BFS() {
	memset(dis, 10, sizeof dis);
	deque<int> q;
	dis[S]=0;
	q.push_back(S);
	while (!q.empty()) {
		int now=q.front();
		q.pop_front();
		for (int i=h[now]; i; i=e[i].next) {
			int y=e[i].to, v=e[i].value;
			if (dis[y]>dis[now]+v) {
				dis[y]=dis[now]+v;
				if (v) q.push_back(y);
				else q.push_front(y);
			}
		}
	}
	for (int i=1; i<=n; i++) printf("%d\n", dis[i]);
}


void build(int &p, int &q, int l, int r) {
	if (l==r) return p=l, q=l, void();
	if (!p) p=++tot;
	if (!q) q=++tot;
	int mid=l+r>>1;
	build(ls[p], ls[q], l, mid), insert(ls[p], p, 0), insert(q, ls[q], 0);
	build(rs[p], rs[q], mid+1, r), insert(rs[p], p, 0), insert(q, rs[q], 0);
}

void add(int p, int l, int r, int x, int y, int z, int flag) {
	if (x<=l && r<=y) {
		if (flag) insert(z, p, 0);
		else insert(p, z, 0);
		return;
	}
	int mid=l+r>>1;
	if (x<=mid) add(ls[p], l, mid, x, y, z, flag);
	if (y>mid) add(rs[p], mid+1, r, x, y, z, flag);
}

int main() {
	n=read(), q=read(), S=read(), tot=n;
	int root1=0, root2=0;
	build(root1, root2, 1, n);
	while (q--) {
		int x=read(), y=read(), z=read(), w=read(), a=++tot, b=++tot;
		insert(a, b, 1);
		add(root1, 1, n, x, y, a, 0);
		add(root2, 1, n, z, w, b, 1);
		a=++tot, b=++tot;
		insert(a, b, 1);
		add(root1, 1, n, z, w, a, 0);
		add(root2, 1, n, x, y, b, 1);
	}
	BFS();
	return 0;
}
```