---
title: 扩展欧几里得（exgcd）
date: 2021-10-02 13:33:00
updated: 2021-10-02 13:33:00
tags:
  - 数学
  - 方程
categories:
  - 算法
comments: true
---
## exgcd

求丢番图方程 $ax+by=c$ 的整数解

可由[裴蜀定理](https://baike.baidu.com/item/%E8%A3%B4%E8%9C%80%E5%AE%9A%E7%90%86/5186593)得**当且仅当** $\gcd(a,b)\mid c$ 时会有整数解

### 推导

我们考虑方程 $ax+by=c$，我们可以化简一下，设 $d=\gcd(a,b)$，我们把 $a,b,c$ 同时除以 $d$，得 $a'x+b'y=c'$，则此时方程结果不变，且 $a,b$ 互质。

我们可以先求出 $a'x+b'y=1$ 的 解，$x,y$ 同时乘以 $c'$，即可得到原方程的一组整数解。

我们考虑化简后的 gcd 的过程，做到最后一定是 $a=1,b=0$，我们对于这个 $ax+by=1$ 很容易构出一组解 $x=1,y=0$，这样，我们只要递归向上回溯，就可求出一组原方程的解。

然后我们考虑每次求出 $(b,a\%b)$ 的解，如何求出 $a,b$ 的解。

原方程：$bx'+(a\%b)y'=1$

把取模转化成除法的形式 ，$bx'+(a-\lfloor\frac{a}{b}\rfloor b)y'=1$

化简：$bx'+ay'-\lfloor\frac{a}{b}\rfloor by'=1$

合并同类项：$ay'+b(x'-\lfloor\frac{a}{b}\rfloor y')=1$

然后惊奇的发现，我们已经求出来了，当 $x=y',y=x'-\lfloor\frac{a}{b}\rfloor y'$ 就是一组解。

这样不断向上递归即可求解，别忘记最后要乘上 $c'$。

代码：

```c++
void exgcd(int a, int b, int &x, int &y) {
	if (b==0) {x=1; y=0; return;}
	exgcd(b, a%b, x, y);
	int t=x; x=y; y=t-a/b*y;
}
```

### 通解

若 $(x_1,y_1)$ 为丢番图方程 $ax+by=c$ 的一组解

则 $(x_1+\frac{kb}{\gcd(a,b)},y_1-\frac{ka}{\gcd(a,b)})$ 为该方程的通解 (k 为整数)

代入方程易证。

### 例题

#### [Luogu P5656 【模板】二元一次不定方程 (exgcd)](https://www.luogu.com.cn/problem/P5656)

这是一道模板题

题目让我们求正整数解

1. 当 $c$ 不是 $\gcd(a,b)$ 的倍数时，无整数解。

2. 当 $x$ 为最小正整数解时，$y$ 最大，若 $y$ 仍小于 $0$ 则无正整数解。

   当 $y$ 为最小正整数解时，$x$ 最大，若 $x$ 仍小于 $0$ 则无正整数解。

3. 当 $x$ 最小时 $y$ 最大，$y$ 最小时 $x$ 最大，正整数解的个数为 $x$ 的最大值和最小值的差除以 $x$ 的变化量即为 $\frac{b}{\gcd(a,b)}$.

代码

```c++
#include <cstdio>
#include <cctype>
#define int long long

using namespace std;

int read() {
	int x=0, f=0; char c=getchar();
	while (!isdigit(c)) f|=c=='-', c=getchar();
	while (isdigit(c)) x=(x<<3)+(x<<1)+(c^48), c=getchar();
	return f ? -x : x;
}

int n;

int gcd(int x, int y) {
	if (!y) return x;
	return gcd(y, x%y);
}

void exgcd(int a, int b, int &x, int &y) {
	if (!b) {x=1, y=0; return;}
	exgcd(b, a%b, x, y);
	int t=x; x=y; y=t-a/b*y;
}

signed main() {
	n=read();
	while (n--) {
		int a=read(), b=read(), c=read();
		int d=gcd(a, b), x, y;
		if (c%d!=0) {printf("-1\n"); continue;}
		a/=d; b/=d; c/=d;
		exgcd(a, b, x, y); x*=c; y*=c;//求出特解
		int mnx=(x%b+b-1)%b+1, delta=(x-mnx)/b, mxy=y+delta*a;
		int mny=(y%a+a-1)%a+1, Delta=(y-mny)/a, mxx=x+Delta*b;
		if (mxy<=0) printf("%lld %lld\n", mnx, mny);
		else printf("%lld %lld %lld %lld %lld\n", (mxx-mnx)/b+1, mnx, mny, mxx, mxy);
	}
	return 0;
}
```

### exgcd 求逆元

我们要求 $a^{-1}a\equiv 1\pmod p$

即 $a^{-1}a-kp=1$，$a,p$ 是已知的，要求 $k,a^{-1}$ ，只有当 $\gcd(a, p)=1$ 时才可以用 exgcd 求。

也就相当于解这个方程，即只要代入 $exgcd(a,p,x,y)$

求出来的 $x$ 即为 $a$ 的逆元。
