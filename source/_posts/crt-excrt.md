---
title: CRT & EXCRT
date: 2022-04-21 19:34:14
updated: 2022-04-21 19:34:14
tags:
  - CRT
  - 方程
categories:
  - 数学
comments: true
---
## 中国剩余定理（CRT）

求解同余方程组：
$$
\\left\\{\\begin{array}{l}x \\equiv a_{1}\\left(\\bmod m_{1}\\right) \\\\x \\equiv a_{2}\\left(\\bmod m_{2}\\right) \\\\x \\equiv a_{3}\\left(\\bmod m_{3}\\right) \\\\\\cdots \\\\x \\equiv a_{n}\\left(\\bmod m_{n}\\right)\\end{array}\\right.
$$
满足 $m_1,m_2,m_3\cdots m_k$ 两两互质，求满足条件的最小 $x$ 。

---

我们可以对于第 $i$ 个方程考虑，只需满足
$$
\\left\\{\\begin{array}{l}x_i \\equiv 0\\left(\\bmod m_{1}\\right) \\\\x_i \\equiv 0\\left(\\bmod m_{2}\\right) \\\\x_i \\equiv 0\\left(\\bmod m_{3}\\right) \\\\\\cdots\\\\x_i \\equiv a_{i}\\left(\\bmod m_{i}\\right) \\\\\\cdots \\\\x_i \\equiv 0\\left(\\bmod m_{n}\\right)\\end{array}\\right.
$$
我们发只要把所有的 $x_i$ 加起来即可满足条件。

我们发现当 $\frac{m_1\cdot m_2\cdot m_3\cdots m_n}{m_i}|x_i$ 且 $x_i\bmod m_i=a_i$ 时满足条件。

令 $M=m_1\cdot m_2\cdot m_3\cdots m_n$，$\frac{M}{m_i}t_i\equiv 1\pmod {m_i}$，由于 $m_i$ 与 $\frac{M}{m_i}$ 互质，$t$ 可用扩展欧几里得算法求解。

其中一解为 $x_i=a_i\cdot \frac{M}{m_i}\cdot t_i$

通解为 $x=k\times M+\sum_{i=1}^n x_i$

### 例题

[模板题](https://www.luogu.com.cn/problem/P1495)

```cpp
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

void exgcd(int a, int b, int &x, int &y) {
	if (!b) return x=1, y=0, void();
	exgcd(b, a%b, x, y);
	int t=x; x=y; y=t-a/b*y;
}

int n, m[11], a[11], M=1, ans;

signed main() {
	n=read();
	for (int i=1; i<=n; i++) {
		m[i]=read(), a[i]=read();
		M*=m[i];
	}
	for (int i=1; i<=n; i++) {
		int k=M/m[i], t, x;
		exgcd(k, m[i], t, x);
		ans=(ans+t*k*a[i])%M;
	}
	printf("%lld\n", (ans+M)%M);
	return 0;
}
```

## 扩展中国剩余定理（EXCRT）

还是求解同余方程组：
$$
\\left\\{\\begin{array}{l}x \\equiv a_{1}\\left(\\bmod m_{1}\\right) \\\\x \\equiv a_{2}\\left(\\bmod m_{2}\\right) \\\\x \\equiv a_{3}\\left(\\bmod m_{3}\\right) \\\\\\cdots \\\\x \\equiv a_{n}\\left(\\bmod m_{n}\\right)\\end{array}\\right.
$$
但是 $m_1,m_2\cdots m_n$ 不一定两两互质，求最小非负整数解。

P.S. 虽然时扩展的，但是好像比不扩展的好写还容易理解qwq

---

由于不互质，我们不能用刚才的方式分开了求解，我们考虑把两个方程合并。

有以下两个方程
$$
\\left\\{\\begin{array}{l}x \\equiv a_{1}\\left(\\bmod m_{1}\\right) \\\\x \\equiv a_{2}\\left(\\bmod m_{2}\\right) \\\\\\end{array}\\right.
$$
所以 $x=m_1\cdot k_1+a_1=m_2\cdot k_2+a_2$

所以 $m_1\cdot k_1-m_2\cdot k_2=a_2-a_1$

由裴蜀定理可得当前仅当 $\gcd(m_1,m_2)|(a_2-a_1)$ 时有整数解

我们可以用 exgcd 求出 $k_1$ 的通解为 $k+\frac{m_2}{\gcd(m_1,m_2)}K$

所以 $x=(k+\frac{m_2}{\gcd(m_1,m_2)}K)m_1+a_1=k_1\cdot m_1+a_1+\frac{m_1\cdot m_2}{\gcd(m_1,m_2)}K$

可得 $x\equiv k\cdot m_1+a_1\pmod {\text{lcm}(m_1,m_2)}$

继续合并即可找到一组解。

### 例题

[模板题](https://www.luogu.com.cn/problem/P4777)

```cpp
#include <cstdio>
#include <cctype>
#include <algorithm>
#define int __int128

using namespace std;

int read() {
	int x=0, f=0; char c=getchar();
	while (!isdigit(c)) f|=c=='-', c=getchar();
	while (isdigit(c)) x=(x<<3)+(x<<1)+(c^48), c=getchar();
	return f ? -x : x;
}

void exgcd(int a, int b, int &x, int &y) {
	if (!b) return x=1, y=0, void();
	exgcd(b, a%b, x, y);
	int t=x; x=y; y=t-a/b*y;
}

int n, M, A, m, a, g, p, t, k;

signed main() {
	n=read(); M=read(), A=read();
	for (int i=2; i<=n; i++) {
		m=read(), a=read(), g=__gcd(M, m), p=m/g;
		exgcd(M, m, k, t);
		k=(k*(a-A)/g%p+p)%p;
		A=k*M+A; M=M/__gcd(M, m)*m;
		A=(A%M+M)%M;
	}
	printf("%lld\n", (long long)A);
	return 0;
}
```

## 奇技淫巧

大数翻倍法

要求同时满足 $x\equiv a_1\pmod {m_1}$，$x\equiv a_2\pmod {m_2}$

我们找到 $m_1,m_2$ 中较大的一个，并把对应的 $a$ 不断加上这个 $m$，直到满足另一个方程，这时候的新 $m$ 即为 $\text{lcm}(m_1,m_2)$。

由于数据范围的限制，合并之后的模数为最小公倍数，所以做的总次数不会很多。

CRT 模板题那题可以拿到满分，EXCRT 模板题只能拿到 $77$ 分。

牛马代码：

```cpp
#include <cstdio>
#include <cctype>
#include <algorithm>
#define int __int128

using namespace std;

int read() {
	int x=0, f=0; char c=getchar();
	while (!isdigit(c)) f|=c=='-', c=getchar();
	while (isdigit(c)) x=(x<<3)+(x<<1)+(c^48), c=getchar();
	return f ? -x : x;
}

int n, M, A;

signed main() {
	n=read(), M=read(), A=read();
	for (int i=2; i<=n; i++) {
		int m=read(), a=read();
		if (m>M) swap(m, M), swap(a, A);
		while (A%m!=a) A+=M;
		M=M/__gcd(M, m)*m;
	}
	printf("%lld\n", (long long)A);
	return 0;
}
```