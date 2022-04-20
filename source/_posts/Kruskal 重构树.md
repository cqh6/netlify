---
title: Kruskal 重构树
date: 2021-10-20 18:23:00
updated: 2021-10-20 18:23:00
tags:
  - 最小生成树
  - Kruskal 重构树
categories:
  - 算法
comments: true
---
## 定义

在做 Kruskal 的过程中，我们加边的时候把边变成一个新建的点，点权即为原来的边权，并将此点向这个边连接的两个点连边，这样我们就会得到一个 $2n-2$ 个点的 Kruskal 重构树。

## 构建

和 Kruskal 十分类似。

合并时新建一个节点，记录权值为边权，并向两个点连边。

代码：

```c++
cnt=n;
for (int i=1; i<=n; i++) fa[i]=i;
sort(e+1, e+m+1, cmp);
for (int i=1; i<=m; i++) {
	int x=find(e[i].x), y=find(e[i].y);
	if (x==y) continue;
	v[++cnt]=e[i].v;
	fa[x]=fa[y]=fa[cnt]=cnt;
	E[x].push_back(cnt);
	E[y].push_back(cnt);
	E[cnt].push_back(x);
	E[cnt].push_back(y);
}
```

## 性质

- Kruskal 重构树是一个二叉堆。

- 原图中两个点所有路径最大边权的最小值 = 最小生成树上两点路径最大值 = Kruskal 重构树上两点之间的 LCA 的权值。
- 原图中的点都成了叶节点，其余的点都为最小生成树上的一条边，有权值。

## 例题

- [Luogu P4768 [NOI2018] 归程](https://www.luogu.com.cn/problem/P4768)
