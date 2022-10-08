---
title: CF1455G Forbidden Value 题解
date: 2022-10-08 18:45:16
updated: 2022-10-08 18:45:16
categories:
  - 题解
comments: true
---
## [Link](https://www.luogu.com.cn/problem/CF1455G)

容易发现每句话的依赖关系构成了一棵树，所有的 `if/end` 为非叶节点，`set` 为叶节点。

执行一个语句时必须执行过父亲。

设 $f_{u,i}$ 表示做完 $u$ 节点，出来时值为 $i$ 时的最小代价。

只需要枚举每个 $u$ 的子节点 $v$，判断 $v$ 是否会被执行即可。

若 $v$ 的操作为 `set`

1. 若 $i\ne y$，则执行完这句话时值为 $i$，说明赋值操作没有执行，需要花费删除这句话 $val$ 的代价，$f_{u,i}=f_{u,i}+val$
  
2. 若 $i=y$，则执行前无论为何值，执行完值都为 $y$，$f_{u,y}=\min\{f_{u,i}\}$，需要对于每个 $u$ 维护一个 $f_{u,i}$ 的最小值。
  

发现对所有 $i\ne y$ 的 $f_{u,i}$ 执行 $+val$ 显然会 TLE，我们可以打一个标记 $\Delta_u$ 表示所有的 $f_{u,i}$ 都会加的值，则 $\Delta_u+=val,f_{u,y}=\min\{f_{u,i}\}-val$，就不需要修改 $i\ne y$ 时数量特别多的 $f_{u,i}$ 了。

此时 $f_{u,i}$ 的真实值为 $f_{u,i}=f_{u,i}+\Delta_u$

若 $v$ 的操作为 `if`

1. 若 $i\ne y$，则分执行 `if` 和不执行 `if` 两种情况
  
  - 若执行，则进去时 $i=y$，则 $f_{u,i}=f_{u,y}+f_{v,i}$
    
  - 若不执行，则进去和出来时一样的，则进去时值也为 $i$，$f_{u,i}=f_{u,i}$
    
  
  则 $f_{u,i}=\min(f_{u,y}+f_{v,i},f_{u,i})$
  
2. 若 $i=y$，发现 `if` 不执行，则进去和出去时的值时一样的，则进去时 $i=y$ 则 `if` 一定会执行，$f_{u,y}=f_{u,y}+f_{v,y}$
  

发现第二种操作很好执行，第一种数量状态数太多，但是总状态数很好，可以考虑启发式合并。

用 map 记录所有存在的状态，若 $f_v$ 的状态数少于 $f_v$ 的状态数，则对于每一个 $f_v$ 的状态合并到 $f_u$ 上。

否则 $f_u$ 的状态数少于 $f_v$ 的状态数，则可以考虑先把 $f_u$ 合并到 $f_v$ 上面，最后再 swap $f_u$ 和 $f_v$ 即可，$f_{u,i}=\min(f_{u,y}+f_{v,i},f_{u,i})=\min(f_{v,i},f_{u,i}-f_{u,y})+f_{u,y}$，此时 $\Delta_u$ 要加上 $f_{u,y}$

注：map 的 swap 是 $O(1)$ 的

~~当然也可以用线段树合并来写~~

## CODE

```cpp
#include<bits/stdc++.h>
#define int long long

using namespace std;

int read() {
	int u=0, f=0; char c=getchar();
	while (!isdigit(c)) f|=c=='-', c=getchar();
	while (isdigit(c)) u=(u<<3)+(u<<1)+(c^48), c=getchar();
	return f ? -u : u;
}

const int N=2e5+10;
int n, s, fa, lst[N], t[N], val[N], y[N], d[N];
vector<int> a[N];
map<int, int> f[N];
multiset<int> mn[N];

void dfs(int u) {
	mn[u].insert(f[u][y[u]]=0);
	for (int v:a[u])
		if (t[v]==1) {
			if (y[v]!=s) {
				if (f[u].count(y[v])) {
					int tmp=f[u][y[v]];
					f[u][y[v]]=(*mn[u].begin())-val[v];
					mn[u].erase(mn[u].find(tmp));
				}
				else f[u][y[v]]=(*mn[u].begin())-val[v];
				mn[u].insert(f[u][y[v]]);
			}
			d[u]+=val[v];
		}
		else if (f[u].count(y[v])) {
			dfs(v);
			int tmp=f[u][y[v]]+d[v];
			if (f[v].size()<=f[u].size()) {
				for (auto [i, cst]:f[v]) {
					if (!f[u].count(i)) f[u][i]=tmp+cst;
					else {
						mn[u].erase(mn[u].find(f[u][i]));
						if (i==y[v]) f[u][i]=tmp+cst;
						}else f[u][i]=min(f[u][i], tmp+cst);
					}
					mn[u].insert(f[u][i]);
				}
			}
			else {
				d[u]+=tmp;
				for (auto [i, cst]:f[u]) {
					if (!f[v].count(i)) f[v][i]=cst-tmp;
					else {
						mn[v].erase(mn[v].find(f[v][i]));
						if (i!=y[v]) f[v][i]=min(f[v][i], cst-tmp);
					}
					mn[v].insert(f[v][i]);
				}
				swap(f[u], f[v]), swap(mn[u], mn[v]);
			}
		}
}

signed main() {
	n=read(), s=read();
	for (int i=1; i<=n; i++) {
		char opt=getchar();
		if (opt=='s') a[fa].push_back(i), t[i]=1, y[i]=read(), val[i]=read();
		else if (opt=='i') y[i]=read(), a[fa].push_back(i), t[i]=2, lst[i]=fa, fa=i;
		else fa=lst[fa], getchar(), getchar(), getchar();
	}
	dfs(0);
	printf("%lld\n", d[0]+(*mn[0].begin()));
	return 0;
}
```