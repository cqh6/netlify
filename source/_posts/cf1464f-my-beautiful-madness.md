---
sticky: ""
title: CF1464F My Beautiful Madness
date: 2022-10-06 11:01:06
updated: 2022-10-06 11:01:06
categories:
  - 题解
comments: true
---
[Link](https://www.luogu.com.cn/problem/CF1464F)

给定一颗大小为 $n(n\le2*10^5)$ 的树，$m(m\le2*10^5)$ 次操作，维护一个初始为空的路径集合 $P$。

定义树上一条路径的$d$邻居（一个点集）$S$为：$x \in S$当前仅当存在一个路径上的点$y$满足 $dis(x,y)\le d$。

操作分为三种：

$1.$输入 $u,v$，在 $P$ 中加入$u$到$v$的路径。

$2.$输入 $u,v$，删除 $P$ 中**一个**$u$到$v$的路径。

（注意$u$ 到 $v$ 的路径与 $v$ 到 $u$ 的路径是相同的，若有多个 $u$ 到 $v$ 的路径只删除一个）

$3.$输入 $d$，询问 $P$ 中所有路径的 $d$ 邻居交集是否为空，若不为空输出Yes，否则输出No。

---

发现若交集不为空，则所有路径的 LCA 深度最大的一个的 $d$ 级祖先一定在集合中。

设这个点为 $u$ ，设这个点为 $u$ ，可以 multiset 快速求出 $u$。

设 $u$ 的 $d$ 级祖先为 $v$，发现若一条路径若不经过 $v$ 的子树一定不合法。

需要保证所有路径都在 $v$ 子树内，考虑树上叉分，路径 $(x,y)$ 只需在 $x,y$ 处 $+1$，$\text{LCA}(x,y)$ 处 $-1$，子树和即为子树内的路径数。

若经过 $v$ 的子树，且 LCA 不在 $v$ 的子树，则说明这条路径一定经过 $v$ 点，一定是合法的。

其他路径的 LCA 都在 $v$ 的子树内，发现 $u$ 到这些路径的最短距离为 $u$ 到它们 LCA 的距离。

只要满足 $u$ 到所有 LCA （LCA 在 $v$ 子树内的）的距离都 $\le d$，发现 $u$ 到这些点的距离最大值 $\le v$，最大值一定为这些点构成的树的直径的一个点。

动态维护直径，每次合并两个连通块时，新直径的两个端点一定在原 $2$ 个直径中的 $4$ 个点中。

## CODE

```cpp
#include <bits/stdc++.h>

using namespace std;

int read() {
    int x=0, f=0; char c=getchar();
    while (!isdigit(c)) f|=c=='-', c=getchar();
    while (isdigit(c)) x=(x<<3)+(x<<1)+(c^48), c=getchar();
    return f ? -x : x;
}

const int N=2e5+10;
int n, q, L[N], R[N], cnt, nowm, id[N], fa[N][21], dep[N], sum[N];
vector<int> e[N];
multiset<pair<int, int> > s;
struct path{int x, y;} t[N<<2];

int LCA(int x, int y) {
    if (dep[x]<dep[y]) swap(x, y);
    for (int i=20; i>=0; i--)
        if (dep[fa[x][i]]>=dep[y]) x=fa[x][i];
    if (x==y) return x;
    for (int i=20; i>=0; i--)
        if (fa[x][i]!=fa[y][i]) x=fa[x][i], y=fa[y][i];
    return fa[x][0];
}

int dis(int x, int y) {
    if (x<0 || y<0) return -1e9;
    return dep[x]+dep[y]-2*dep[LCA(x, y)];
}

path operator + (path a, path b){
    int x=-1, y=-1, d=-1e9;
    if (dis(a.x,a.y)>d) d=dis(a.x, a.y), x=a.x, y=a.y;
    if (dis(a.x,b.x)>d) d=dis(a.x, b.x), x=a.x, y=b.x;
    if (dis(a.x,b.y)>d) d=dis(a.x, b.y), x=a.x, y=b.y;
    if (dis(a.y,b.x)>d) d=dis(a.y, b.x), x=a.y, y=b.x;
    if (dis(a.y,b.y)>d) d=dis(a.y, b.y), x=a.y, y=b.y;
    if (dis(b.x,b.y)>d) d=dis(b.x, b.y), x=b.x, y=b.y;
    return {x,y};
}

void dfs(int x, int f) {
    fa[x][0]=f;
    dep[x]=dep[f]+1;
    for (int i=1; i<=20; i++) fa[x][i]=fa[fa[x][i-1]][i-1];
    L[x]=++cnt, id[cnt]=x;
    for (int y:e[x]) if (y!=f) dfs(y, x);
    R[x]=cnt;
}

namespace tree {
    int c[N];
    void add(int x, int y) {
        while (x<=n) {
            c[x]+=y;
            x+=x&-x;
        }
    }
    int ask(int x) {
        int r=0;
        while (x) {
            r+=c[x];
            x-=x&-x;
        }
        return r;
    }
    int ask(int l, int r) {
        return ask(r)-ask(l-1);
    }
}

void build(int x, int l, int r) {
    t[x]={-1, -1};
    if (l==r) return;
    int mid=l+r>>1;
    build(x<<1, l, mid);
    build(x<<1|1, mid+1, r);
}

void change(int x, int l, int r, int p, int op) {
    if (l==r) {
        if (op==1) t[x]={id[l], id[l]};
        else t[x]={-1, -1};
        return;
    }
    int mid=l+r>>1;
    if (mid>=p) change(x<<1, l, mid, p, op);
    else change(x<<1|1, mid+1, r, p, op);
    t[x]=t[x<<1]+t[x<<1|1];
}

path ask(int x, int l, int r, int L, int R) {
    if (l>=L && r<=R) return t[x];
    int mid=l+r>>1;
    if (mid<L) return ask(x<<1|1, mid+1, r, L, R);
    if (mid+1>R) return ask(x<<1, l, mid, L, R);
    return ask(x<<1, l, mid, L, R)+ask(x<<1|1, mid+1, r, L, R);
}

void add(int x) {
    if (!sum[x]) change(1, 1, n, L[x], 1);
    sum[x]++;
}
void del(int x) {
    sum[x]--;
    if (!sum[x]) change(1, 1, n, L[x], 0);
}

int FA(int x, int d) {
    if (dep[x]<=d) return 1;
    for (int i=20; i>=0; i--)
        if (d>>i&1) x=fa[x][i];
    return x;
}

int ask(int d) {
    int v=FA((*s.rbegin()).second, d), t=FA(v, d);
    if (tree::ask(L[t], R[t])!=nowm) return 0;
    path p=ask(1, 1, n, L[t], R[t]);
    if (dis(v, p.x)<=d && dis(v, p.y)<=d) return 1;
    return 0;
}

int main() {
    n=read(), q=read();
    for (int i=1; i<n; i++) {
        int x=read(), y=read();
        e[x].push_back(y);
        e[y].push_back(x);
    }
    dfs(1, 0), build(1, 1, n);
    while (q--) {
        int op=read();
        if (op==1) {
            int x=read(), y=read(), z=LCA(x, y);
            tree::add(L[x], 1);
            tree::add(L[y], 1);
            tree::add(L[z], -1);
            add(z), nowm++;
            s.insert({dep[z], z});
        }
        else if (op==2) {
            int x=read(), y=read(), z=LCA(x, y);
            tree::add(L[x], -1);
            tree::add(L[y], -1);
            tree::add(L[z], 1);
            del(z), nowm--;
            s.erase(s.lower_bound({dep[z], z}));
        }
        else if (op==3) {
            int d=read();
            puts(ask(d)?"Yes":"No");
        }
    }
    return 0;
}
```