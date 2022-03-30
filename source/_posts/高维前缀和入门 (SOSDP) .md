---
index_img: https://img.xjh.me/random_img.php?type=bg&ctype=nature&return=302&seed=683841
title: 高维前缀和入门 (SOSDP) 
date: 2022-01-22 09:05:00
updated: 2022-01-22 09:05:00
tags:
  - DP
  - SOSDP
categories:
  - 算法
comments: true
---
## SOSDP

SOSDP 主要用于解决高维前缀和问题。

我们先来看一下低维前缀和是怎么求的

一般写二维前缀和是这样写的：

```cpp
for (int i=1; i<=n; i++)
    for (int j=1; j<=n; j++)
        s[i][j]=s[i][j-1]+s[i-1][j]-s[i-1][j-1]+a[i][j]
```

当然也可以先求出每个行单独的前缀和，再按列求求出二维前缀和：

```cpp
for (int i=1; i<=n; i++)
    for (int j=1; j<=n; j++)
        s[i][j]=s[i][j-1]+a[i][j];
for (int i=1; i<=n; i++)
    for (int j=1; j<=n; j++)
        s[i][j]=s[i-1][j]+s[i][j];
```

三维前缀和如果直接展开的话会很长，所以我们也可以分维度求：

```cpp
for (int i=1; i<=n; i++)
    for (int j=1; j<=n; j++)
        for (int k=1; k<=n; k++)
            s[i][j][k]=s[i][j][k-1]+a[i][j][k];
for (int i=1; i<=n; i++)
    for (int j=1; j<=n; j++)
        for (int k=1; k<=n; k++)
            s[i][j][k]=s[i][j-1][k]+s[i][j][k];
for (int i=1; i<=n; i++)
    for (int j=1; j<=n; j++)
        for (int k=1; k<=n; k++)
            s[i][j][k]=s[i-1][j][k]+s[i][j][k];
```

我们一般遇到的多维前缀和一般每个维度只有 $0,1$，即对于每个 $i$ 求 $\sum_{j\subset i}a_j$

高维前缀和也可以用类似这样求，我们可以把整个状态压缩成一个 mask ，然后类似上面的方法求出每一维度的前缀和。

```cpp
for (int i=0; i<n; i++)
	for (int j=0; j<1<<n; j++)
		if (j>>i&1) f[j]+=f[j^(1<<i)];
```

好像太水了qwq

### 例题

#### [CF165E Compatible Numbers](https://www.luogu.com.cn/problem/CF165E)

emmmmmmmmmm，板子题

如果一个数 $x\&y=0$ ，那么 $y$ 一定是 $x$ 取反后的子集，用高维前缀和求出每个状态子集中存在的最大值即可。

#### 代码

```cpp
#include <cstdio>
#include <cctype>
#include <algorithm>

using namespace std;

const int N=1<<22;
int n, a[N], f[N];

int read() {
	int x=0, f=0; char c=getchar();
	while (!isdigit(c)) f|=c=='-', c=getchar();
	while (isdigit(c)) x=(x<<3)+(x<<1)+(c^48), c=getchar();
	return f ? -x : x;
}

int main() {
	n=read();
	for (int i=1; i<=n; i++) {
		a[i]=read();
		f[a[i]]=a[i];
	}
	for (int i=0; i<=21; i++)
		for (int j=0; j<N; j++)
			if (j>>i&1) f[j]=max(f[j^(1<<i)], f[j]);
	for (int i=1; i<=n; i++)
		printf("%d ", f[(N-1)^a[i]]?f[(N-1)^a[i]]:-1);
	return 0;
}
```

### 更多题目

#### 1. [CF449D Jzzhu and Numbers](https://www.luogu.com.cn/problem/CF449D)：容斥+高维前缀和

#### 2. [CF1208F Bits And Pieces](https://www.luogu.com.cn/problem/CF1208F)：高维前缀和大水题

#### 3. [ARC100E Max Sum](https://atcoder.jp/contests/arc100/tasks/arc100_c)：高维前缀和大水题