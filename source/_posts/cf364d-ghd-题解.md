---
title: CF364D Ghd 题解
date: 2022-05-10 15:53:15
updated: 2022-05-10 15:53:15
tags:
  - 随机
  - gcd
categories:
  - 题解
comments: true
---
> **题意**
>
> 给定 $n$ 个数，求至少取出 $\left\lceil\frac{n}{2}\right\rceil$ 个数时的 $\gcd$ 的最大值。

我们假设一个数 $x$ 被选取，则 $\gcd$ 一定为 $x$ 的一个约数。

我们可以算出每个约数是多少个其他数的约数，如果 $\ge\left\lceil\frac{n}{2}\right\rceil$ 即可成为答案。

一个数和 $x$ 共有的约数为他们 $\gcd$ 的倍数，可以直接暴力做。

我们发现每个数被取到的概率 $\ge\frac{1}{2}$，可以随机选取 $T$ 个数，钦定这个数一定被选取，算一次最大答案。

每个数有 $\ge\frac{1}{2}$ 的正确性，正确率 $\ge1-\frac{1}{2^T}$。

时间复杂度：$O(T(\sqrt {a_{max}}+n\log d(x)+d^2(x)))$，$d(x)$ 为 $x$ 约数个数。

由下表（网上贺的）

| $n\le$       | $10$ | $10^2$ | $10^3$ | $10^4$ | $10^5$ | $10^6$ | $10^7$ | $10^8$ | $10^9$ |
| ------------ | ---- | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ |
| $\max{d(n)}$ | $4$  | $12$   | $32$   | $64$   | $128$  | $240$  | $448$  | $768$  | $1344$ |

| $n\le$       | $10^{10}$ | $10^{11}$ | $10^{12}$ | $10^{13}$ | $10^{14}$ | $10^{15}$ | $10^{16}$ | $10^{17}$ | $10^{18}$ |
| ------------ | --------- | --------- | --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| $\max{d(n)}$ | $2304$    | $4032$    | $6720$    | $10752$   | $17280$   | $26880$   | $41472$   | $64512$   | $103680$  |

发现 $d(x)$ 会达到很大，瓶颈在 $d^2(x)$ 上，所以 $T$ 不能取到很大，经过尝试可得到，当 $T=10$ 时可以卡过。

正确率 $\ge\frac{1023}{1024}$，可以过掉本题。

注：`rand()`的范围是 $0-32767$，随机选取一个数时不能直接用 `rand()`，后面的数会取不到。

## 代码

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

const int N=1e6+10;
int n, d[N], a[N], cnt[N], ans;

signed main() {
	srand(time(0));
	n=read();
	for (int i=1; i<=n; i++) a[i]=read();
	for (int T=1; T<=10; T++) {
		int x=a[(rand()<<15|rand())%n+1], y=x, m=0;
		for (int i=1; i*i<=y; i++)
			if (y%i==0) {
				d[++m]=i;
				if (i*i!=y) d[++m]=y/i;
			}
		sort(d+1, d+m+1);
		for (int i=1; i<=m; i++) cnt[i]=0;
		for (int i=1; i<=n; i++) cnt[lower_bound(d+1, d+m+1, __gcd(x, a[i]))-d]++;
		for (int i=1; i<=m; i++)
			for (int j=i+1; j<=m; j++)
				if (d[j]%d[i]==0) cnt[i]+=cnt[j];
		for (int i=m; i>=1; i--)
			if (cnt[i]*2>=n) {ans=max(ans, d[i]); break;}
	}
	printf("%lld\n", ans);
	return 0;
}
```

