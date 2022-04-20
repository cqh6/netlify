---
title: BSGS & EXBSGS
date: 2021-11-04 14:26:00
updated: 2021-11-04 14:26:00
tags:
  - BSGS
  - EXBSGS
categories:
  - 数学
comments: true
---
## BSGS(大步小步算法)

主要用于求解形如 $a^x\equiv b\pmod q$ 的问题（$a,b,p$ 已知，$a,p$ 互质，求 $x$）

### 解法

既然叫大步小步，那肯定和大步小步有关（瞎扯）。

简单来说就是一种类似分块的方法。

我们发现答案一定在 $0\sim p-1$ 之间，因为 $a^{p-1}\equiv 1\pmod p$，即 $p-1$ 为一循环节。

于是，我们把 $x$ 在 $0\sim p-1$ 之间方程 $t=\left\lceil\sqrt{p}\right\rceil$ 块，每块大小为 $t$，多出来的部分不用管它，因为超出了循环节，后面出现的数前面一定会出现。

对于每个 $x=0\sim p-1$，$a^x$ 都可以表示为 $a^{i\cdot t-j}$，$i$ 为 $x$ 所在块的编号，$j$ 为 $x$ 在它的块中的倒序排序。可推得：
$$
a^{i\cdot t-j}\equiv  b\pmod p\Rightarrow a^{i\cdot t}\equiv b\cdot a^j\pmod p
$$
可以用一个 hash 数组存起来每一个 $b\cdot a^j$ 对应的 $j$

然后我们依次枚举每一个块，如果答案在这个块中，计算出 $a^{i\cdot t}=a^{(i-1)t}\cdot a^t$ ，前面的为上个块的数，后面的是一个定值，可以预处理出来。

如果在 hash 数组中找到，就找到了答案；如果所有块都做完，还没找到答案则无解。

### 算法流程

1. 计算第一个区间中 $b\cdot a^j$ 对应的 $j$，可以用 hash 或 map 来实现。
2. 令 $tmp=a^t$
3. 枚举每个区间，计算出 $a^{i\cdot t}$，判断是否在这个区间中
4. 如果答案在这个区间中，直接返回答案
5. 找完所有块还找不到答案说明无解

### 时间复杂度

当 $t=\sqrt{p}$ 时，时间复杂度最小，为 $O(\sqrt{p})$

如果用 map 来实现，时间复杂度还要乘上一个 $\log$

### 代码

```c++
int power(int x, int y, int p) {
	int r=1;
	while (y) {
		if (y&1) r=r*x%p;
		x=x*x%p; y>>=1;
	}
	return r;
}

int bsgs(int a, int b, int p) {
	unordered_map<int, int> f;
	int t=ceil(sqrt(p));
	for (int i=1; i<=t; i++) b=b*a%p, f[b]=i;
	int tmp=power(a, t, p); b=1;
	for (int i=1; i<=t; i++) {
		b=b*tmp%p;
		if (f.count(b)) return i*t-f[b];
	}
	return -1;
}
```

### 例题

#### [P3846 [TJOI2007] 可爱的质数/【模板】BSGS](https://www.luogu.com.cn/problem/P3846)

模板题，直接套用模板即可。

#### 代码

```c++
#include <cstdio>
#include <cctype>
#include <unordered_map>
#include <cmath>
#define int long long

using namespace std;

int a, b, p;

int read() {
	int x=0, f=0; char c=getchar();
	while (!isdigit(c)) f|=c=='-', c=getchar();
	while (isdigit(c)) x=(x<<3)+(x<<1)+(c^48), c=getchar();
	return f ? -x : x;
}

int power(int x, int y, int p) {
	int r=1;
	while (y) {
		if (y&1) r=r*x%p;
		x=x*x%p; y>>=1;
	}
	return r;
}

int bsgs(int a, int b, int p) {
	unordered_map<int, int> f;
	int t=ceil(sqrt(p));
	for (int i=1; i<=t; i++) b=b*a%p, f[b]=i;
	int tmp=power(a, t, p); b=1;
	for (int i=1; i<=t; i++) {
		b=b*tmp%p;
		if (f.count(b)) return i*t-f[b];
	}
	return -1;
}

signed main() {
	p=read(), a=read(), b=read();
	int ans=bsgs(a, b, p);
	if (ans==-1) printf("no solution\n");
	else printf("%lld\n", ans);
	return 0;
}
```

## EXBSGS(扩展大步小步算法)

我们需要求一个最小自然数 $x$，满足 $a^x\equiv b\pmod p$，$p$ 为任意整数。

### 解法

我们考虑什么时候不能用 BSGS 来求，在以下式子中我们需要两个互为充要条件，则右边的推到左边的需要满足存在 $a^j$ 的逆元，只有当 $a,p$ 互质时才满足条件。
$$
a^{i\cdot t-j}\equiv  b\pmod p\Longleftrightarrow  a^{i\cdot t}\equiv b\cdot a^j\pmod p
$$
可以发现，当 $a\perp p$ 时，仍然可做，可以用 exgcd 或欧拉定理求出逆元，直接做 BSGS 即可。

当 $\gcd(a,p) \ne 1$ 时我们把方程转化：$a^x+kp=b$，由裴蜀定理可得，当 $\gcd(a,p)\nmid b$ 时无解。

令 $g=\gcd(a,p)$，两边同时除以 $g$ ，得 $\frac{a}{g}\cdot a^{x-1}+k\cdot\frac{p}{g}=\frac{b}{g}$，即 $\frac{a}{g}\cdot a^{x-1}\equiv\frac{b}{g}\pmod {\frac{p}{g}}$

每次令 $x=x-1,b=\frac{b}{g}\cdot{(\frac{a}{g})}^{-1},p=\frac{p}{g}$

不断这样做，直到 $\gcd(a,p)=1$ 为止，再做 BSGS 即可。

因为每次要乘以 $\frac{a}{g}$ 的逆元，比较慢，我们可以先保留着，到最后一起乘，这样并不会影响 $\gcd$ 的值，所以不影响正确性。

### 代码

```c++
int gcd(int a, int b) {
	if (!b) return a;
	return gcd(b, a%b);
}

int power(int x, int y, int p) {
	int r=1;
	while (y) {
		if (y&1) r=r*x%p;
		x=x*x%p; y>>=1;
	}
	return r;
}

void exgcd(int a, int b, int &x, int &y) {
	if (!b) { x=1; y=0; return; }
	exgcd(b, a%b, x, y);
	int t=x; x=y; y=t-a/b*y;
}

int inv(int x, int p) {
	int inv, k;
	exgcd(x, p, inv, k);
	return (inv%p+p)%p;
}

int bsgs(int a, int b, int p) {
	map<int, int> f;
	int t=ceil(sqrt(p));
	for (int i=1; i<=t; i++) b=b*a%p, f[b]=i;
	int tmp=power(a, t, p); b=1;
	for (int i=1; i<=t; i++) {
		b=b*tmp%p;
		if (f.count(b)) return i*t-f[b];
	}
	return -1;
}

int exbsgs(int a, int b, int p) {
	if (b==1 || p==1) return 0;
	int g, tot=0, c=1;
	while ((g=gcd(a, p))>1) {
		if (b%g) return -1;
		b/=g; p/=g; c=c*(a/g)%p; tot++;
		if (b==c) return tot;
	}
	int res=bsgs(a, b*inv(c, p)%p, p);
	if (res==-1) return -1;
	return res+tot;
}
```

### 例题

#### [P4195 【模板】扩展 BSGS/exBSGS](https://www.luogu.com.cn/problem/P4195)

模板题，套模板即可。



#### 代码

```c++
#include <cstdio>
#include <cctype>
#include <map>
#include <cmath>
#define int long long

using namespace std;

int a, b, p;

int read() {
	int x=0, f=0; char c=getchar();
	while (!isdigit(c)) f|=c=='-', c=getchar();
	while (isdigit(c)) x=(x<<3)+(x<<1)+(c^48), c=getchar();
	return f ? -x : x;
}

int gcd(int a, int b) {
	if (!b) return a;
	return gcd(b, a%b);
}

int power(int x, int y, int p) {
	int r=1;
	while (y) {
		if (y&1) r=r*x%p;
		x=x*x%p; y>>=1;
	}
	return r;
}

void exgcd(int a, int b, int &x, int &y) {
	if (!b) { x=1; y=0; return; }
	exgcd(b, a%b, x, y);
	int t=x; x=y; y=t-a/b*y;
}

int inv(int x, int p) {
	int inv, k;
	exgcd(x, p, inv, k);
	return (inv%p+p)%p;
}

int bsgs(int a, int b, int p) {
	map<int, int> f;
	int t=ceil(sqrt(p));
	for (int i=1; i<=t; i++) b=b*a%p, f[b]=i;
	int tmp=power(a, t, p); b=1;
	for (int i=1; i<=t; i++) {
		b=b*tmp%p;
		if (f.count(b)) return i*t-f[b];
	}
	return -1;
}

int exbsgs(int a, int b, int p) {
	if (b==1 || p==1) return 0;
	int g, tot=0, c=1;
	while ((g=gcd(a, p))>1) {
		if (b%g) return -1;
		b/=g; p/=g; c=c*(a/g)%p; tot++;
		if (b==c) return tot;
	}
	int res=bsgs(a, b*inv(c, p)%p, p);
	if (res==-1) return -1;
	return res+tot;
}

signed main() {
	while (a=read(), p=read(), b=read(), a&&b&&p) {
		int ans=exbsgs(a%p, b%p, p);
		if (ans==-1) printf("No Solution\n");
		else printf("%lld\n", ans);
	}
	return 0;
}
```
