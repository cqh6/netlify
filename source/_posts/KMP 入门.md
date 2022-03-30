---
index_img: https://img.xjh.me/random_img.php?type=bg&ctype=nature&return=302&seed=432712
title: KMP 入门
date: 2021-10-20 18:21:00
updated: 2021-10-20 18:21:00
tags:
  - 字符串
  - KMP
categories:
  - 算法
comments: true
---
## KMP

KMP可以在线性的时间内求出一个字符串是否为另一个字符串的子串，也可以求出每次出现的位置

我们先来康一道模板题：[P3375 【模板】KMP字符串匹配](https://www.luogu.com.cn/problem/P3375)

它让我们求 $s_2$ 在 $s_1$ 中所有出现的位置。以及 $s_2$ 每个前缀的 $next$ 数组。

我们定义字符串 $s$ 的 $next[i]$ 为 $s[1\sim i]$ 最长前缀和后缀相等的长度。

即：$next[i]=\max j$，$j<i$ 且 $s[1\sim j]=s[i-j+1\sim i]$

我们假设现在已经求出了 $next[i-1]$ ，如果 $s[i]==s[next[i-1]+1]$，那么$next[i]=next[i-1]+1$。

> 证明：我们假设$next[i]$可以更大，那么去除第$i$位和第$next[i]$之后还是相等的，那么$next[i-1]$就会更大。

那如果当前不匹配，我们需要缩小$next[i]$，直接暴力枚举肯定很慢，我们发现如果当前候选匹配长度$j$无法匹配，那么下一次匹配的一定是$next[j]$，这样只要不断检验下去。

> 证明：我们假设当前长度为$j$，存在$next[j]<k<j$，前 $k$ 位可以和后 $k$ 位相等。
>
> 那么显然，前 $j$ 位和后 $j$ 位的最后 $k$ 个相等，从而前 $k$ 位和前 $j$ 位的最后 $k$ 位相等。
>
> 所以 $next[j]$ 可以等于 $k$，且比之前的 $next[k]$ 最大矛盾，所以不存在这样的 $k$ 。

所以，这样我们就完成的字符串 $s2$ 的自我匹配。

代码如下：

```c++
for (int i=2, j=0; i<=n; i++)  {
	while (j>0 && s2[i]!=s2[j+1]) j=next[j];
	if (s2[i]==s2[j+1]) j++;
	next[i]=j;
}

```

我们再定义 $f$ 数组表示 $s1$ 的每个前缀的后缀和 $s2$ 前缀相等的最大长度。

那么，每个 $f[i]=s2.size()$ ，$s2$ 就在 $i-s2.size()+1$ 的位置出现一次。

$f$ 数组的求法和 $next$ 数组的求法基本相同，代码如下：
```c++
for (int i=1, j=0; i<=m; i++) {
	while (j>0 && (j==n || s1[i]!=s2[j+1])) j=next[j];
	if (s1[i]==s2[j+1]) j++;
	if ((f[i]=j)==n) printf("%d\n", i-n+1);
}
```
完整代码
```c++
#include <cstdio>
#include <cstring>
using namespace std;

const int N=1e6+10;
int next[N], f[N];
char s1[N], s2[N];

int main() {
	scanf("%s%s", s1+1, s2+1);
	int n=strlen(s2+1), m=strlen(s1+1);
	next[1]=0;
	for (int i=2, j=0; i<=n; i++)  {
		while (j>0 && s2[i]!=s2[j+1]) j=next[j];
		if (s2[i]==s2[j+1]) j++;
		next[i]=j;
	}
	for (int i=1, j=0; i<=m; i++) {
		while (j>0 && (j==n || s1[i]!=s2[j+1])) j=next[j];
		if (s1[i]==s2[j+1]) j++;
		if ((f[i]=j)==n) printf("%d\n", i-n+1);
	}
	for (int i=1; i<=n; i++) printf("%d ", next[i]);
	return 0;
}
```

时间复杂度：因为每次 $j$ 最多增加 $1$ ，$j$ 减小比变大慢，所以总时间复杂度位 $O(n+m)$。

## 参考
1. [OI-wiki](https://oi-wiki.org/string/kmp/)
2. 《算法竞赛进阶指南》