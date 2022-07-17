---
title: "[AGC028B] Removing Blocks 题解"
date: 2022-07-17 17:23:23
updated: 2022-07-17 17:23:23
tags:
  - 概率期望
  - 笛卡尔树
categories:
  - 题解
comments: true
---
### [题目链接](https://www.luogu.com.cn/problem/AT4436)

先按删除时间建立一棵笛卡尔树，发现树的深度 $h_i$ 即表示一条线段对答案的贡献。

我们需要求每个点的期望深度，只要求它的期望祖先个数，即为每个点为它祖先的概率之和。

对于点 $i$，若 $j<i$ ，$j$ 为 $i$ 的祖先当且仅当 $j+1,j+1\cdots i$ 被删除的时间晚于 $j$，则概率为 $\frac{1}{i-j+1}$，若 $j>i$，则概率为 $j-i+1$。

$E(h_{i})=\sum\limits_{j=1}^{i-1} \frac{1}{i-j+1}+\sum\limits_{i+1}^{n} \frac{1}{j-i+1}+1$

预处理调和级数即可。

### CODE

```cpp
#include <bits/stdc++.h>
#define int long long

using namespace std;

int read() {
	int x=0, f=0; char c=getchar();
	while (!isdigit(c)) f|=c=='-', c=getchar();
	while (isdigit(c)) x=(x<<3)+(x<<1)+(c^48), c=getchar();
	return f ? -x : x;
}

const int N=1e5+10, P=1e9+7;
int n, m, ans, a[N], sum[N], inv[N];

signed main() {
	n=read(), inv[1]=sum[1]=1;
	for (int i=1; i<=n; i++) a[i]=read();
	for (int i=2; i<=n; i++) inv[i]=inv[P%i]*(P-P/i)%P, sum[i]=(sum[i-1]+inv[i])%P;
	for (int i=1; i<=n; i++) ans=(ans+(sum[i]+sum[n-i+1]-1)*a[i]%P)%P;
	for (int i=1; i<=n; i++) ans=ans*i%P;
	printf("%lld\n", ans);
	return 0;
}
```