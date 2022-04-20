---
title: CF367E Sereja and Intervals 题解
date: 2021-11-09 10:58:00
updated: 2021-11-09 10:58:00
tags:
  - DP
categories:
  - 题解
comments: true
---
## [题目](https://www.luogu.com.cn/problem/CF367E)

有 $n$ 个区间，你需要为每个区间分配左右端点，端点属于 $[1,m]\cap \mathbb{N}$。你需要保证区间两两互不包含且至少存在一个区间的左端点等于 $x$，输出方案数对 $10^9+7$ 取模的结果，区间有标号。$nm\leqslant 10^5$，$1\leqslant x\leqslant m$。

## 题解

我们先考虑如何确定一个状态，发现当在这些点上取出 $2n$ 个点，并确定它们是左端点还是右端点。

如果当前状态可以匹配，当且仅当任意前缀左端点个数 $\ge$ 右端点个数。

那么我们一定可以确定一个唯一状态，即左右端点的匹配是确定的。

证明：

> 我们可以给每一个左端点按坐标从小到大编号，右端点也按坐标从小到大编号，那么一定是编号相同的左右端点匹配。
>
> 如果一个编号小的左端点和编号大的右端点匹配，那么一定存在一个编号比它大的左端点和一个编号比它小右端点匹配，那么就存在了包含关系。
>
> 如下图：红色为选定的左端点，蓝色为选定的右端点，为一组不合法方案。
>
> ![](https://cdn5.maocdn.cn/img/2021/11/09/1.png)

然后这样就很好做了，我们只要确定一组合法的左右端点的位置就可以恰好对应一组答案。

然后考虑每一个位置可以放什么：

发现每个位置放左、右端点的个数都不可能超过 $1$，如果当前位置左端点个数超过 $1$ ，即存在两个区间左端点相同，那么它们一定存在包含关系，如下图：

![](https://cdn5.maocdn.cn/img/2021/11/09/2.png)

所以，每个位置只有以下四种选择：

1. 不放（当这个位置不为 $x$ 时）
2. 放一个左端点
3. 放一个右端点（当这个位置不为 $x$ 时）
4. 放一个左端点和右端点

然后就可以 DP 了，设 $f[i][j][k]$ 表示到了第 $i$ 个位置，放了 $j$ 个左端点，$k$ 个右端点的方案数，就可以用上面的四种方案转移了。

时间复杂度：$O(n^2m)$

即有解时当且仅当 $n\le m$，所以数据范围就很小了！

## 代码

```c++
#include <cstdio>
#include <cctype>
#include <cstring>

using namespace std;

int read() {
	int x=0, f=0; char c=getchar();
	while (!isdigit(c)) f|=c=='-', c=getchar();
	while (isdigit(c)) x=(x<<3)+(x<<1)+(c^48), c=getchar();
	return f ? -x : x;
}

const int p=1e9+7;
int n, m, x;

int main() {
	n=read(), m=read(), x=read();
	if (n>m) return printf("0\n"), 0;
	int f[m+10][n+10][n+10];
	memset(f, 0, sizeof f);
	f[0][0][0]=1;
	for (int i=1; i<=m; i++)
		for (int j=0; j<=n; j++)
			for (int k=0; k<=j; k++) {
				if (j>k) f[i][j][k]=(f[i][j][k]+f[i-1][j-1][k])%p;
				if (j && k) f[i][j][k]=(f[i][j][k]+f[i-1][j-1][k-1])%p;
				if (i!=x) {
					if (k) f[i][j][k]=(f[i][j][k]+f[i-1][j][k-1])%p;
					f[i][j][k]=(f[i][j][k]+f[i-1][j][k])%p;
				}
			}
	int fac=1;
	for (int i=1; i<=n; i++) fac=1ll*fac*i%p;
	printf("%d\n", 1ll*f[m][n][n]*fac%p);
	return 0;
}
```
