---
index_img: https://img.xjh.me/random_img.php?type=bg&ctype=nature&return=302&seed=587192
title: LuoguP8250 交友问题 题解
date: 2022-03-25 19:02:00
updated: 2022-03-25 19:02:00
categories:
  - 题解
comments: true
---
> [题目链接](https://www.luogu.com.cn/problem/P8250)
>
> 洛谷上有 $n$ 位用户。这些用户组成了一个双向的网络。
>
> 洛谷的图片分享机制如下：如果第 $i$ 个用户向他的好友 $j$ 分享了一张照片，那么，$j$ 的所有好友 $k$ 就能看到这张照片。**$j$ 也可以看到这张照片。**
>
> 现在，用户 $u_i$ 想分享一张照片，但是TA不想让用户 $v_i$ 看到这张照片。在不发送给自己的情况下，TA想知道，他最多可以发送给多少位好友？
>
> $1 \le n,q \le 2\times10^5，1\le m \le 7\times 10^5$
>
> **保证没有重边和自环**

我们可以先把题目转换一下，改为与 $u$ 相邻的总点数减去同时与 $u,v$ 相邻的点数，如果 $u$ 和 $v$ 相邻还要减 $1$，所以我们只要解决求同时与 $u,v$ 相邻的点数就好了。

我们可以先把所有的点分为 $2$ 类。

1. 度数不超过 $\sqrt m$ 的点。
2. 度数大于 $\sqrt m$ 的点。

然后，我们~~很容易~~发现，第 $2$ 类点不超过 $\sqrt m$ 个，否则边数就会比 $m$ 大。

因为个数不多，我们可以给每一个第二类点开一个 bitset，记录它与每个点是否相邻。

这样我们可以对每一次询问分类讨论：

- 两个点都属于第 $1$ 类

  我们把与一个点相邻的标记一遍，再扫一遍与第二个相邻的点，统计有多少个点被标记过。

- 一个点属于第 $1$ 类，一个点都属于第 $2$ 类

  我们可以扫描一遍与第 $1$ 类点相邻的所有点，直接用第 $2$ 类点的 bitset 判断是否和这个点相邻。

- 两个点都属于第 $2$ 类点

  我们要求有多少个点同时与这两个点相邻，也就是求与这两个点交集的大小。

  我们直接把它们的 bitset 取一个交集，求出它的大小就好了！

好像挺水的，时间复杂度：$O(n\sqrt m+q\sqrt m+\frac{n\cdot q}{w})$

### CODE

```cpp
#include <cstdio>
#include <cctype>
#include <vector>
#include <bitset>
#include <cmath>

using namespace std;

int read() {
	int x=0, f=0; char c=getchar();
	while (!isdigit(c)) f|=c=='-', c=getchar();
	while (isdigit(c)) x=(x<<3)+(x<<1)+(c^48), c=getchar();
	return f ? -x : x;
}

const int N=2e5+10, T=sqrt(7e5)+10;
int n, m, q, t, id[N], tot, vis[N];
vector<int> e[N];
bitset<N> f[T];

int main() {
	n=read(), m=read(), q=read(), t=sqrt(m);
	for (int i=1; i<=m; i++) {
		int x=read(), y=read();
		e[x].push_back(y);
		e[y].push_back(x);
	}
	for (int i=1; i<=n; i++)
		if (e[i].size()>t) {
			id[i]=++tot;
			for (int j:e[i]) f[id[i]][j]=1;
		}
	while (q--) {
		int x=read(), y=read(), ans=e[x].size();
		if (e[x].size()>e[y].size()) swap(x, y);
		if (id[x]) printf("%d\n", ans-(f[id[x]]&f[id[y]]).count()-f[id[x]][y]);
		else if (id[y]) {
			for (int z:e[x]) ans-=f[id[y]][z];
			printf("%d\n", ans-f[id[y]][x]);
		}
		else {
			for (int z:e[x]) vis[z]=1;
			if (vis[y]) ans--;
			for (int z:e[y]) ans-=vis[z];
			for (int z:e[x]) vis[z]=0;
			printf("%d\n", ans);
		}
	}
	return 0;
}
```