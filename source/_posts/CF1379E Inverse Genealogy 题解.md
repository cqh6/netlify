---
index_img: https://img.xjh.me/random_img.php?type=bg&ctype=nature&return=302&seed=423621
title: CF1379E Inverse Genealogy 题解
date: 2021-10-30 09:22:00
updated: 2021-10-30 09:22:00
tags:
  - 构造
categories:
  - 题解
comments: true
---
提供另一种比较暴力的方法

## 题目描述

自己看[题面](https://www.luogu.com.cn/problem/CF1379E)

## 分析

我们先考虑不合法的情况，显然有以下几种：

1. 当 $n$ 为偶数时，一定存在只有一个儿子的节点，不满足条件。

2. 不平衡点数量超过上界，即 $\max(\lfloor\frac{n-3}{2}\rfloor)$，在以下情况中可以达到上界：

   ![](https://z3.ax1x.com/2021/10/30/5v4yfU.png)

3. 当不平衡点个数为 $0$ 时，只有满二叉树满足条件，当前节点个数无法构成满二叉树一定不合法。

4. 当不平衡点个数为 $1$ 时，只有完全二叉树满足条件，当前节点个数无法构成完全二叉树当且仅当节点个数可以构成满二叉树。

5. 特殊情况：$n=9,k=2$，发现这种情况也无法构出来。

我们发现，当不满足以上所有条件时，一定可以构出满足条件的树来。

我们还发现，不满足条件的树的个数很少，即大部分的树满足条件。

所以我们考虑一种比较暴力的方法，每次枚举左子数的大小和不平衡点的个数，计算出右子数的大小和不平衡点的个数，判断如果合法，就往分别左右子树递归。

这种方法比较暴力，虽然代码长了一点点，但是比较简单。

## 时间复杂度

最坏时间复杂度：每次找到最后才找到合法解，$O(n^2k)$，但是这绝对不可能达到，大部分时候在小常数次就可以找到合法解。

实际时间复杂度：$O(n)$ 左右

## 代码

具体实现在代码中有注释

```c++
#include <cstdio>
#include <cctype>
#include <algorithm>
 
using namespace std;
 
int read() {
	int x=0, f=0; char c=getchar();
	while (!isdigit(c)) f|=c=='-', c=getchar();
	while (isdigit(c)) x=(x<<3)+(x<<1)+(c^48), c=getchar();
	return f ? -x : x;
}
 
const int N=1e5+10;
int n, k, fa[N], tot;
 
bool full(int x) {//判断当前节点个数是否可以构成满二叉树
	x++;
	return (x-(x&-x))==0;
}
 
bool check(int n, int k){//判断当前节点个数对应的不平衡点个数是否可以构造出来
	int mx=max((n-3)/2, 0);
	if (k>mx || n%2==0 || n==9 && k==2) return 0;
	if (full(n) && k==1 || !full(n) && k==0) return 0;
	return 1;
}

void build(int n, int k, int father) {//n, k, father: 当前节点个数，不平衡点个数，以及当前节点的父亲（输出答案要用）
	if (n==3) {//边界
		fa[tot+1]=father;
		fa[tot+2]=tot+1;
		fa[tot+3]=tot+1;
		tot+=3;
		return;
	}
	fa[++tot]=father; n--; father=tot;
	for (int i=1; i<=n/2; i++)//枚举左子树点的个数
		for (int j=0; j<=k; j++)//枚举左子树不平衡点的个数
			if (i*2>n-i && check(i, j) && check(n-i, k-j)) return build(i, j, father), build(n-i, k-j, father), void();//当前节点不是不平衡点
			else if (i*2<=n-i && j<k && check(i, j) && check(n-i, k-1-j)) return build(i, j, father), build(n-i, k-1-j, father), void();//当前节点是不平衡点
}
 
int main() {
	n=read(), k=read();
	if (!check(n, k)) return puts("NO"), 0;
	puts("YES");
	build(n, k, 0);
	for (int i=1; i<=n; i++) printf("%d ", fa[i]);
	printf("\n");
	return 0;
}
```