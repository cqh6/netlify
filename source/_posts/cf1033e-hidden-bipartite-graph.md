---
sticky: ""
title: CF1033E Hidden Bipartite Graph
date: 2022-08-18 18:59:08
updated: 2022-08-18 18:59:08
tags:
  - 二分
  - 随机
categories:
  - 题解
comments: true
---
> [你谷 link](https://www.luogu.com.cn/problem/CF1033E)
> 
> [CF Link](https://codeforces.com/problemset/problem/1033/E)
> 
> 题意：交互题，给定一个 $n(1\le n\le 600)$ 个点连通图，你每次可以询问一个点集 $s$，系统会返回在 $s$ 中有两个端点的边的数量。你最多可以询问 $20000$ 次。你需要判断这个图是不是一个二分图。
> 
> 若是二分图，则输出其中一部分点数和所有点的编号。

先考虑构造任意一棵生成树，原图是二分图当且仅当奇数层内部之间没有边，偶数层内部之间也没有边。

考虑对未加入生成树上的点进行分治，若点集 $s$ 中存在一条到点 $x$ 的遍，则考虑把 $s$ 分成 $2$ 个集合 $s_1$，$s_2$ 继续寻找寻找所有边，否则当前集合就不存在到 $x$ 的边。

计算点集 $s$ 与 $x$ 之间的边数其实很简单，只要计算 $s$ 和 $x$ 的并集中内部的边数减去 $s$ 内部的边数。

就可以在 $O(n\log n)$ 找出一颗生成树。

若判断出原图是一个二分图，则奇数层和偶数层就位二分图的两部分。

否则考虑找奇环，若奇数层内存在边，则考虑找到这条边，加上两端点到它们 LCA 的路径就可以构成一个奇环。

我们找遍可以利用随机化的思想，每次把当前确定存在边的集合中随机取 $\frac{n}{2}$ 个，则期望计算 $4$ 次就可以把集合缩小到原来的一半了

这个的复杂度为 $O(n\cdot k)$，$k$ 为小常数。

**CODE**

```cpp
#include <bits/stdc++.h>

using namespace std;

int read() {
	int x=0, f=0; char c=getchar();
	while (!isdigit(c)) f|=c=='-', c=getchar();
	while (isdigit(c)) x=(x<<3)+(x<<1)+(c^48), c=getchar();
	return f ? -x : x;
}

const int N=605;
int n, fa[N], dep[N], vis[N];
queue<int> q;

int ask(vector<int> v) {
	int n=v.size();
	if (n<=1) return 0;
	printf("? %d\n", n);
	for (int i=0; i<n; i++) printf("%d ", v[i]);
	puts(""), fflush(stdout);
	return read();
}

void add(int u, int f) {
	if (vis[u]) return;
	vis[u]=1, dep[u]=dep[f]+1;
	fa[u]=f, q.push(u);
}

void calc(int u, vector<int> v) {
	if (!v.size()) return;
	int m1=ask(v), m2;
	v.push_back(u), m2=ask(v);
	if (m1==m2) return;
	v.pop_back();
	if (v.size()==1) return add(v.front(), u);
	int mid=v.size()/2;
	vector<int> v1, v2;
	for (int i=0; i<mid; i++) v1.push_back(v[i]);
	for (int i=mid; i<v.size();  i++) v2.push_back(v[i]);
	calc(u, v1), calc(u, v2);
}

void find(int u, int v) {
	int lca=0, a=u, b=v;
	while (a!=b) {
		if (dep[a]<dep[b]) swap(a, b);
		a=fa[a];
	}
	lca=a;
	vector<int> ans;
	for (int i=u; i; i=fa[i]) {
		ans.push_back(i);
		if (i==lca) break;
	}
	reverse(ans.begin(), ans.end());
	for (int i=v; i!=lca; i=fa[i]) ans.push_back(i);
	printf("N %d\n", ans.size());
	for (int v:ans) printf("%d ", v);
	exit(0);
}

void solve(vector<int> v) {
	if (v.size()<=1) return;
	if (v.size()==2) return find(v.front(), v.back());
	while (1) {
		random_shuffle(v.begin(), v.end());
		int mid=(v.size()+1)/2;
		vector<int> v1=v; v1.resize(mid);
		if (ask(v1)) return solve(v1);
	}
}

int main() {
	n=read(), add(1, 0);
	while (!q.empty()) {
		int u=q.front(); q.pop();
		vector<int> v;
		for (int i=1; i<=n; i++)
			if (!vis[i]) v.push_back(i);
		calc(u, v);
	}
	vector<int> vec[2];
	for (int i=1; i<=n; i++) vec[dep[i]&1].push_back(i);
	if (ask(vec[0])) solve(vec[0]);
	else if (ask(vec[1])) solve(vec[1]);
	printf("Y %d\n", vec[0].size());
	for (int v:vec[0]) printf("%d ", v);
	return 0;
}
```