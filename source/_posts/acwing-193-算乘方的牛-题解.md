---
index_img: https://img.xjh.me/random_img.php?type=bg&ctype=nature&return=302&seed=913312
title: Acwing 193. 算乘方的牛 题解
date: 2022-04-04 18:58:38
updated: 2022-04-04 18:58:38
tags:
  - 搜索
categories:
  - 题解
comments: true
---
[题目链接](https://www.acwing.com/problem/content/195/)

写着是困难题，然而并不难qwq

我们可以把题目转换一下，底数相同的乘法变为指数上的加法。

题目转换为通过 $0$ 和 $1$ 经过最少次数的加法或减法变为 $P$ 。

很容易发现操作过程中产生 $0$ 或负数是不优的，直接跳过。

数据不大，考虑 DFS。

剪枝：

- 估价函数：显然最少次数不低于 $\log$ 次，可以用 IDA* 来优化。
- $x,y$ 通过加法和减法变成的数一定为 $\gcd(x,y)$ 的倍数，如果 $P$ 不为 $\gcd(x,y)$ 的倍数显然不能到达。

## 代码

```cpp
#include <cstdio>
#include <cctype>
#include <algorithm>

using namespace std;

int read() {
	int x=0, f=0; char c=getchar();
	while (!isdigit(c)) f|=c=='-', c=getchar();
	while (isdigit(c)) x=(x<<3)+(x<<1)+(c^48), c=getchar();
	return f ? -x : x;
}

int n, dep;

bool dfs(int x, int u, int v) {
	if (x>dep) return 0;
	if (u>v) swap(u, v);
	if ((v<<dep-x)<n) return 0;
	if (n%__gcd(u, v)) return 0;
	if (u==v) return 0;
	if (v==n) return 1;
	for (int i=0; i<4; i++)
	return dfs(x+1, u+v, u)||dfs(x+1, u+v, v)||dfs(x+1, v-u, u)||dfs(x+1, v-u, v)||dfs(x+1, u+u, u)||dfs(x+1, u+u, v)||dfs(x+1, v+v, u)||dfs(x+1, v+v, v);
}

int main() {
	n=read();
	while (!dfs(0, 0, 1)) dep++;
	printf("%d\n", dep);
	return 0;
}
```

