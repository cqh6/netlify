---
title: CF1662F Antennas 题解
date: 2022-07-17 16:38:03
updated: 2022-07-17 16:38:03
tags:
  - 线段树
  - 最短路
categories:
  - 题解
keywords: []
comments: true
---
### [题目链接](https://www.luogu.com.cn/problem/CF1662F)

显然 $b\in[a-p_a,a+p+a]\wedge a\in[b-p_b,b+p_b]$ 是 $a,b$ 连同的充要条件。

我们建一棵线段树，叶子节点 $x$ 记录 $mn=x-p_x$ 和 $mx=x+p_x$，非叶子节点记录它所有儿子的 $mn$ 的最小值和 $mx$ 的最大值。

考虑 BFS，从原点开始，一个点 $x$ 能到达的点为在区间 $[x-p_x,x+p_x]$ 中 $x\in[mn,mx]$ 的点。直接在线段树上找所有满足 $x\in[mn,mx]$ 的区间跳到叶子节点。并把 $mn=\inf,mx=-\inf$，这样可以保证以后不会在做到。

因为如果 $x\in[mn,mx]$，则子树内一定存在一个满足条件的点，则最多 $O(\log n)$  次就可以找到一个合法的点。

### CODE

```cpp
#include <bits/stdc++.h>
#define ls p<<1
#define rs ls|1

using namespace std;

int read() {
	int x=0, f=0; char c=getchar();
	while (!isdigit(c)) f|=c=='-', c=getchar();
	while (isdigit(c)) x=(x<<3)+(x<<1)+(c^48), c=getchar();
	return f ? -x : x;
}

const int N=2e5+10;
int n, s, t, L[N], R[N], dis[N], mx[N<<2], mn[N<<2];
queue<int>q;

void pushup(int p) {
	mn[p]=min(mn[ls], mn[rs]);
	mx[p]=max(mx[ls], mx[rs]);
}

void build(int p, int l, int r) {
	if (l==r) return mn[p]=L[l], mx[p]=R[l], void();
	int mid=l+r>>1;
	build(ls, l, mid);
	build(rs, mid+1, r);
	pushup(p);
}

void update(int p, int l, int r, int s, int t, int v) {
	if (mx[p]<v || mn[p]>v) return;
	if (l==r) {
		if (dis[l]>=0) return;
		dis[l]=dis[v]+1, mn[p]=1e9, mx[p]=-1e9;
		q.push(l);
		return;
	}
	int mid=l+r>>1;
	if (s<=mid) update(ls, l, mid, s, t, v);
	if (t>mid) update(rs, mid+1, r, s, t, v);
	return pushup(p);
}

void clear(int n) {
	for (int i=1; i<=n<<2; i++) mn[i]=1e9, mx[i]=-1e9;
}

int bfs(int n, int s, int t) {
	for (int i=1; i<=n; i++) dis[i]=-1;
	dis[s]=0; q.push(s);
	while (!q.empty()) {
		int p=q.front(); q.pop();
		update(1, 1, n, L[p], p-1, p);
		update(1, 1, n, p+1, R[p], p);
	}
	return dis[t];
}

int main() {
	int T=read();
	while (T--) {
		n=read(), s=read(), t=read();
		clear(n);
		for (int i=1; i<=n; i++) {
			int x=read();
			L[i]=max(1, i-x);
			R[i]=min(n, i+x);
		}
		build(1, 1, n);
		printf("%d\n", bfs(n, s, t));
	}
	return 0;
}
```