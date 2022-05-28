---
title: CF1438F Olha and Igor 题解
date: 2022-05-11 09:57:52
updated: 2022-05-11 09:57:52
tags:
  - 交互
  - 随机
categories:
  - 题解
comments: true
---
> **题意**
>
> 有一颗深度为 $h$ 的完全二叉树，共有 $n=2^h-1$ 个节点。
>
> 你可以最多询问 $n+420$ 次：
>
> - 询问 `? u v w` 会返回在以 $u$ 为根的树内 $v,w$ 的 $\tt LCA$
> - 给出答案 `! r` 表示你已经确定的答案。

我们发现 $\tt LCA$ 有一个性质，在以 $u$ 为根的子树内，$v,w$ 的 $\tt LCA$ 为子树上为在整棵树上到 $u,v,w$ 距离和最小的点。

显然，这样的点只有一个，且交换 $u,v,w$ 后不会影响答案。

我们考虑如果知道了 $dep=2$ 的所有节点，即为根节点的两个儿子 `p1 p2`，只要再询问这两个点和其他点的组合 `p1 p2 i`，如果返回结果为 $i$ ，则 $i$ 为根节点，这样只要 $n-2$ 次即可找到答案。

还剩下最少 $422$ 次，我们发现每次随机询问 $3$ 个节点，询问结果出现次数最多的两个点为 $dep=2$ 的两个点的概率十分的大，具体证明可以看下面感性理解一下qwq

一共有 $\binom{n}{3}=\frac{n\times(n-1)\times(n-2)}{6}$ 种取法，假设一个点 $x$ 的深度为 $dep$（根节点深度为 $1$）

- 若 $x$ 为根节点，则当且仅当询问时一个节点为 $x$，另外两个在两个子树中各选一个时返回为 $x$，概率为 $\frac{\left (\frac{(n-1)}{2}  \right ) ^2}{\binom{n}{3}}=\frac{3n-3}{2n^2-4n}$，我们发现当 $n$ 很大时概率会很小，当 $h=18$ 时，$n=2^{18}-1$，概率约为 $5.7\times 10^{-6}$。
- 若 $x$ 为叶子节点，则返回结果为 $x$ 的概率为 $0$。
- 对于其他节点，查询结果为它的概率为 $\frac{(2^{h-dep}-1)^2\cdot(2^h-2^{h-dep+1})+2\cdot(2^h-2^{h-dep+1})\cdot(2^{h-dep}-1)+(2^{h-dep}-1)^2}{\frac{n(n-1)(n-2)}{6}}$ ，~~式子懒得化简了~~，我们发现当 $dep=2$ 时的概率大大大于其他的概率，当 $h=18,dep=2$ 时概率约为 $18.75\%$，当 $dep\ne2$ 的点的概率最大不超过 $10\%$。

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

map<int, int> cnt;
int mx1, mx2, p1, p2;

int random() {
    return rand()<<15|rand();
}

int main() {
    srand(time(0));
    int h=read(), n=(1<<h)-1;
    for (int i=1; i<=422; i++) {
        int x=random()%n+1, y=random()%n+1, z=random()%n+1;
        while (x==y) y=random()%n+1;
        while (x==z || y==z) z=random()%n+1;
        printf("? %d %d %d\n", x, y, z);
        fflush(stdout);
        cnt[read()]++;
    }
    for (auto i:cnt)
        if (i.second>mx1) mx2=mx1, p2=p1, mx1=i.second, p1=i.first;
        else if (i.second>mx2) mx2=i.second, p2=i.first;
    for (int i=1; i<=n; i++)
        if (i!=p1 && i!=p2) {
            printf("? %d %d %d\n", i, p1, p2);
            fflush(stdout);
            if (i==read()) {
                printf("! %d\n", i);
                fflush(stdout);
                return 0;
            }
        }
    return 0;
}
```