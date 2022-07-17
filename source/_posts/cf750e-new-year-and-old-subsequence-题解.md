---
title: CF750E New Year and Old Subsequence 题解
date: 2022-07-17 16:39:38
updated: 2022-07-17 16:39:38
tags:
  - 动态动态规划
categories:
  - 题解
comments: true
---
### [题目链接](https://www.luogu.com.cn/problem/CF750E)

### 题意

多次询问一个字符串的一个子串至少删去多少个字符后才能使得其不包含子序列 `2016` 而包含 `2017`。

设 $f_{i,0}$ 表示前 $i$ 位不出现 `2` 的最少删除的个数。

$f_{i,1}$ 表示前 $i$ 位出现 `2` 并且不出现 `20` 的最少删除的个数。

$f_{i,2-4}$ 同理，很显然可以得到转移：

$$
\left\{\begin{array}{l}
f_{i, 0}=f_{i-1,0}+\left[s_{i}=2\right] \\
f_{i, 1}=\min \left(f_{i-1,1}+\left[s_{i}=0\right], f_{i-1,0}\left[s_{i}=2\right]\right) \\
f_{i, 2}=\min \left(f_{i-1,2}+\left[s_{i}=1\right], f_{i-1,1}\left[s_{i}=0\right]\right) \\
f_{i, 3}=\min \left(f_{i-1,3}+\left[s_{i}=7 \vee s_{i}=6\right], f_{i-1,2}\left[s_{i}=1\right]\right) \\
f_{i, 4}=\min \left(f_{i-1,4}+\left[s_{i}=6\right], f_{i-1,3}\left[s_{i}=7\right]\right)
\end{array}\right.
$$

发现这玩意可以直接用矩阵乘法求。

因为是动态的，只需用线段树维护矩阵乘即可。

### CODE

```cpp
#include <bits/stdc++.h>
#define lp p<<1
#define rp p<<1|1

using namespace std;

int read() {
	int x=0, f=0; char c=getchar();
	while (!isdigit(c)) f|=c=='-', c=getchar();
	while (isdigit(c)) x=(x<<3)+(x<<1)+(c^48), c=getchar();
	return f ? -x : x;
}

const int N=2e5+10, M=5;
int n, m;
char s[N];

struct matrix{
	int v[5][5];
	void clear() {memset(v, 0x3f, sizeof v);}
	void init() {clear(); v[0][0]=v[1][1]=v[2][2]=v[3][3]=v[4][4]=0;}
	matrix operator * (const matrix b) const {
		matrix c; c.clear();
		for (int i=0; i<5; i++)
			for (int k=0; k<5; k++)
				for (int j=0; j<5; j++)
					c.v[i][j]=min(c.v[i][j], v[i][k]+b.v[k][j]);
		return c;
	}
} t[N<<2];

void build(int p, int l, int r) {
	if (l==r) {
		t[p].init();
		if (s[l]=='2') t[p].v[0][1]=0, t[p].v[0][0]=1;
		if (s[l]=='0') t[p].v[1][2]=0, t[p].v[1][1]=1;
		if (s[l]=='1') t[p].v[2][3]=0, t[p].v[2][2]=1;
		if (s[l]=='7') t[p].v[3][4]=0, t[p].v[3][3]=1;
		if (s[l]=='6') t[p].v[3][3]=1, t[p].v[4][4]=1;
		return;
	}
	int mid=l+r>>1;
	build(lp, l, mid);
	build(rp, mid+1, r);
	t[p]=t[lp]*t[rp];
}

matrix query(int p, int l, int r, int a, int b) {
	if (a<=l && r<=b) return t[p];
	int mid=l+r>>1;
	matrix res; res.init();
	if (a<=mid) res=query(lp, l, mid, a, b);
	if (b>mid) res=res*query(rp, mid+1, r, a, b);
	return res;
}

int main() {
	n=read(), m=read(), scanf("%s", s+1);
	build(1, 1, n);
	while (m--) {
		int l=read(), r=read();
		int ans=query(1, 1, n, l, r).v[0][4];
		printf("%d\n", ans==0x3f3f3f3f?-1:ans);
	}
	return 0;
}
```