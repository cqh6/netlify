---
index_img: https://img.xjh.me/random_img.php?type=bg&ctype=nature&return=302&seed=842124
title: CF1578L Labyrinth 题解
date: 2021-10-28 21:11:00
updated: 2021-10-28 21:11:00
tags:
  - 最小生成树
  - Kruskal 重构树
categories:
  - 题解
comments: true
---
## 题意

[题目链接](https://www.luogu.com.cn/problem/CF1578L)

给定一张图, 每条边有一个宽度 $w_i$ , 表示只有你的宽度 $\le w_i$ 时才能穿过. 每个点有一个糖, 每个糖吃掉后会让你变胖 $a_i$ . 你从 $1$ 号点出发, 到达一个点时选择暂时不吃这个糖, 也可以选择立刻吃掉这个糖. 问你初始时最胖能是多宽, 使得存在一个方案, 从 $1$ 出发, 将所有点糖全部吃掉 (不需要回到 $1$ 号点). $1\le n,m\le10^5,1\le a_i,w_i\le10^9$

## 题解

显然，我们经过的所有边一定是最大生成树上的边。

然后我们发现，对于当前树上最小的边，删去这条边后剩下两个联通块，一定是先吃完一个联通块的点，在走过这条边，并吃完另一个连通块的点。

> 证明：
>
> 我们假设删除这条边后会剩下两个联通块 $u$ , $v$。
>
> 假设我们先吃掉联通块 $u$ 的一部分，并走过这条边，吃完联通块 $v$，再走回来后吃完连通块 $u$。
>
> 如果这种方案成立，因为吃东西只会使体重增加，那么如果先吃完 $v$ ，再走回来吃完 $u$ 这种方案一定是可行的，所以这种方案更优。

因此，我们可以将原图转换成一棵 Kruskal 重构树，联通块问题转换为了子树问题。

我们设 $f_u$ 表示以 $u$ 为根的子树内，当前处于 $u$ 位置，当前最多可以多胖，使得可以吃完这棵子树，$sum_u$ 表示以 $u$ 为根的子树内所有点的权值和。

假设当前用一条权值为 $v$ 的边合并子树 $x$、$y$，并在 Kruskal 重构树上新建了 $z$ 节点，那么如果先吃 $x$ 子树，那么必须满足一下两个条件：

- 吃完后可以回到 $z$ 节点，并吃掉 $z$ 节点（原来是连接 $x$ 子树和 $y$ 子树的边），所以 $f_z+s_x\le v$

- 吃完之后必须可以吃完 $y$ 子树，所以 $f_z+s_x\le f_y$。

所以转移为 $f_z=\min(f_y,v)-s_x$ ，还有可能先吃 $y$ 子树，只需在两者之间取一个最大值即可。

## 代码

```c++
#include <cstdio>
#include <cctype>
#include <algorithm>
#define int long long

using namespace std;

const int N=2e5+10;
int n, m, cnt, c[N], fa[N], f[N], sum[N];
struct edge{int x, y, z;} e[N];

bool operator<(const edge &a, const edge &b) {return a.z>b.z;}

int find(int x) {return x==fa[x] ? x : fa[x]=find(fa[x]);}

int read() {
	int x=0, f=0; char ch=getchar();
	while (!isdigit(ch)) f|=ch=='-', ch=getchar();
	while (isdigit(ch)) x=(x<<3)+(x<<1)+(ch^48), ch=getchar();
	return f ? -x : x;
}

signed main() {
	n=cnt=read(), m=read();
	for (int i=1; i<=n; i++) c[i]=read();
	for (int i=1; i<=m; i++)
		e[i].x=read(), e[i].y=read(), e[i].z=read();
	sort(e+1, e+m+1);
	for (int i=1; i<=n; i++) fa[i]=i, f[i]=1e18, sum[i]=c[i];
	for (int i=1; i<=m; i++) {
		int x=find(e[i].x), y=find(e[i].y);
		if (x==y) continue;
		cnt++;
		fa[x]=fa[y]=fa[cnt]=cnt;
		f[cnt]=max(min(e[i].z, f[y])-sum[x], min(e[i].z, f[x])-sum[y]);
		sum[cnt]=sum[x]+sum[y];
	}
	if (f[cnt]<=0) f[cnt]=-1;
	printf("%lld\n", f[cnt]);
	return 0;
}
```