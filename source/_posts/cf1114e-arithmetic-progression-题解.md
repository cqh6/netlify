---
title: CF1114E Arithmetic Progression 题解
date: 2022-05-11 07:49:33
updated: 2022-05-11 07:49:33
tags:
  - 随机
  - gcd
  - 交互
categories:
  - 题解
comments: true
---
> **题意**
>
> [链接](https://www.luogu.com.cn/problem/CF1114E)
>
> 有一个长度为 $n$ 的序列 $a$，保证它从小到大排序后是个等差数列。你不知道这个序列长什么样，但你可以询问：
>
> - `? i` 询问 $a_i$ 的值
> - `> x` 询问是否存在大于 $x$ 的数。
>
> 最多询问 $60$ 次。
>
> 求出这个等差数列的首项和公差。
>
> $2\le n\le 10^6,0\le a_i\le 10^9$

显然，我们可以用二分答案+操作 $2$ 询问出区间的最大值，最多会询问 $30$ 次。

还剩下 $30$ 次的询问，我们发现公差 $d|(a_{max}-a_i)$

如果我们随机选取 $30$ 个数，发现 $d|\gcd(a_{max}-a_{rand})$

考虑 $d\ne \gcd$ 的概率，即当 $kd|\gcd(a_{max}-a_{rand}),k>1$

概率大约为 $\sum_{i=1}^n \frac{1}{i^{30}}$，官方题解给出的出错概率大约为 $1.86\times 10^{-9}$

注：`rand()` 的范围是 $0-32767<n$，需手写`rand()`

### CODE

```cpp
#include <bits/stdc++.h>

using namespace std;

int read() {
	int x=0, f=0; char c=getchar();
	while (!isdigit(c)) f|=c=='-', c=getchar();
	while (isdigit(c)) x=(x<<3)+(x<<1)+(c^48), c=getchar();
	return f ? -x : x;
}

bool check(int x) {
	printf("> %d\n", x);
	fflush(stdout);
	return read();
}

int calcmax() {
	int l=1, r=1e9, ans;
	while (l<=r) {
		int mid=l+r>>1;
		if (check(mid)) l=mid+1;
		else ans=mid, r=mid-1;
	}
	return ans;
}

int main() {
	int n=read(), mx=calcmax(), d=0;
	for (int i=1; i<=30; i++) {
		int x=(rand()<<15|rand())%n+1;
		printf("? %d\n", x);
		fflush(stdout);
		x=read();
		d=__gcd(d, mx-x);
	}
	printf("! %d %d\n", mx-d*(n-1), d);
	fflush(stdout);
	return 0;
}
```

