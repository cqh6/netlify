---
title: CF990G GCD Counting 题解
date: 2022-07-17 16:09:47
updated: 2022-07-17 16:09:47
tags:
  - 莫反
categories:
  - 题解
comments: true
---


### [Link](https://www.luogu.com.cn/problem/CF990G)

我们可以先求出 $k|\gcd(x, y)$ 的个数，再用莫反。

用并查集维护出每个边能被 $k$ 整除的连通块的大小，若大小为 $s$，则这个联通块的方案数为 $\frac{s\cdot(s+1)}{2}$

最后再用莫反计算答案，好像还挺简单的/kk

### CODE

（好像不是我的代码）

```cpp
#include<bits/stdc++.h>
using namespace std;
const int NR=2e5+10;
#define ll long long
int n,a[NR],flag[NR],fa[NR];
vector<int>g[NR],v[NR];
#define pb push_back

ll f[NR],size[NR];
int get(int x){
	if(fa[x]==x)return x;
	return fa[x]=get(fa[x]);
}
void merge(int x,int y){
	x=get(x);y=get(y);
	if(x==y)return;
	fa[x]=y;size[y]+=size[x];
}
void init(){
	for(int i=1;i<=n;i++)size[i]=1,fa[i]=i;
}

signed main(){
	cin>>n;init();
	for(int i=1;i<=n;i++){
		scanf("%d",&a[i]);
		for(int j=1;j<=sqrt(a[i])+0.5;j++)
			if(a[i]%j==0){
				if(j*j==a[i])v[j].pb(i);
				else v[j].pb(i),v[a[i]/j].pb(i);
			}
	}
	for(int i=1,x,y;i<n;i++)
		scanf("%d%d",&x,&y),g[x].pb(y),g[y].pb(x);
	for(int i=1;i<=2e5;i++){
		for(int x:v[i])flag[x]=1;
		for(int x:v[i])
			for(int k:g[x])
				if(flag[k])merge(x,k);
		for(int x:v[i])
			if(fa[x]==x)f[i]+=size[x]*(size[x]+1)/2;
		for(int x:v[i])
			flag[x]=0,size[x]=1,fa[x]=x;
	}
	for(int i=2e5;i>=1;i--)
		for(int j=2;j*i<=2e5;j++)f[i]-=f[i*j];
	for(int i=1;i<=2e5;i++)
		if(f[i])printf("%d %lld\n",i,f[i]);
	return 0;
}
```