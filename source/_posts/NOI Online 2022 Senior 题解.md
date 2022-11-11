---
title: NOI Online 2022 Senior 题解
date: 2022-03-26 18:23:00
updated: 2022-03-26 18:23:00
categories:
  - 题解
comments: true
---
## T1 丹钓战

> 有 $n$ 个二元组 $(a_i, b_i)$，编号为 $1$ 到 $n$。 
>
> 有一个初始为空的栈 $S$，向其中加入元素 $(a_i, b_i)$ 时，先不断弹出栈顶元素直至栈空或栈顶元素 $(a_j , b_j)$ 满足 $a_i \neq a_j$ 且 $b_i < b_j$，然后再将其加入栈中。
>
>  如果一个二元组入栈后栈内只有这一个元素，则称该二元组是“成功的”。 
>
> 有 $q$ 个询问 $[l_i, r_i]$，表示若将编号在 $[l_i, r_i]$ 中的二元组按编号从小到大依次入栈，会有多少个二元组是“成功的”。 
>
> 询问之间相互独立。
>
> $1\le n,q\le 5\times10^5$

P.S. 很妙的题目名，“丹钓战”=单调栈（谐音梗）。

我们考虑如果把 $[l,r]$ 的二元组依次放入栈中。

如果一个二元组是“成功的”，那么从 $l$ 开始入栈，它入栈时栈是空的。

如果从第 $1$ 个开始入栈，显然它入栈时栈顶的元素一定在 $l$ 前面或不存在。

所以我们可以先预处理出 $f_i$ 表示从第 $1$ 个开始放，第 $i$ 个入栈的时候栈顶的元素是哪一个。

问题就转化为求 $\sum_{i=l}^{r}[f_i<l]$

然后可以把询问离线，按 $l$ 排序。

对于每个 $l_i=l$ 的询问，我们可以直接查询在 $[l_i,r_i]$ 中有多少数 $\le l-1$，用树状数组维护即可。

对于每次 $l\to l+1$，我们把所有 $f_i=l$ 的位置 $+1$ 。

时间复杂度：$O(n\log n)$

### CODE

```cpp
#include <bits/stdc++.h>
#define fre(x) freopen(#x".in", "r", stdin); freopen(#x".out", "w", stdout)

using namespace std;

int read() {
	int x=0, f=0; char c=getchar();
	while (!isdigit(c)) f|=c=='-', c=getchar();
	while (isdigit(c)) x=(x<<3)+(x<<1)+(c^48), c=getchar();
	return f ? -x : x;
}

const int N=5e5+10;
int n, q, a[N], b[N], c[N], ans[N];
vector<pair<int, int> > e[N];
vector<int> v[N];
stack<int> s;

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

int main() {
	// fre(stack);
	n=read(), q=read();
	for (int i=1; i<=n; i++) a[i]=read();
	for (int i=1; i<=n; i++) b[i]=read();
	for (int i=1; i<=n; i++) {
		while (!s.empty() && (a[s.top()]==a[i] || b[s.top()]<=b[i])) s.pop();
		if (!s.empty()) v[s.top()].push_back(i);
		else add(i, 1);
		s.push(i);
	}
	for (int i=1; i<=q; i++) {
		int l=read(), r=read();
		e[l].push_back(make_pair(i, r));
	}
	for (int i=1; i<=n; i++) {
		for (pair<int, int> j:e[i]) ans[j.first]=ask(j.second)-ask(i-1);
		for (int j:v[i]) add(j, 1);
	}
	for (int i=1; i<=q; i++) printf("%d\n", ans[i]);
	return 0;
}
```

## T2 讨论

> 有 $n$ 个人正在打模拟赛，模拟赛有 $n$ 道题目。 
>
> 有两人都会的题目并且没有人会的题目包含另一个人时，两者之间才会讨论。 
>
> （定义第 $i$ 个人会的题目的集合为 $S_i$ ，即当 $S_x\cap S_y\neq\varnothing\land S_x\not\subseteq S_y\land S_y\not\subseteq S_x$ 时，第 $x$ 人和第 $y$ 人会讨论） 
>
> 为了让模拟赛的效果更好，希望你可以找出一对会讨论的人或判断不存在。
>
> $\sum n\le10^6,1\le m\le 2\times10^6$

### Sol#1

我们把每个人会的题目转化为一个集合。

我们考虑按集合大小从小到达插入每一个集合。

这样的好处是当前插入了集合 $S_i$，如果之前的某个集合 $S_j$ 和 $S_i$ 有交集，且 $S_j$ 存在至少一个 $S_i$ 不存在的元素，则 $S_i$ 一定存在至少一个 $S_j$ 不存在的元素。

所以，我们每次插入集合 $S_i$ 的时候，只要判断 $S_i$ 中的每一个元素在前面的某个集合中是否存在 $S_i$ 不存在的元素。

如果满足条件，则 $S_i$ 一定可以和某个集合满足题目中的条件，通过暴力找出那个集合即可。

- 判断某个元素所在的之前的集合是否有其它元素，用一个并查集维护，如果 $x$ 和 $y$ 在同一个集合中，则表示某个包含 $x$ 的集合包含 $y$

- 判断存面的某个集合中是否存在 $S_i$ 不存在的元素，只要判断和 $S_i$ 有交集的集合是否真包含 $S_i$ 就好了！

时间复杂度：$O(m\cdot k)$，$k$ 为并查集复杂度。

#### CODE

```cpp
#include <cstdio>
#include <cctype>
#include <vector>
#define fre(x) freopen(#x".in", "r", stdin); freopen(#x".out", "w", stdout)

using namespace std;

int read() {
	int x=0, fa=0; char c=getchar();
	while (!isdigit(c)) fa|=c=='-', c=getchar();
	while (isdigit(c)) x=(x<<3)+(x<<1)+(c^48), c=getchar();
	return fa ? -x : x;
}

const int N=1e6+10;
int n, vis[N], fa[N], sz[N];
vector<int> v[N], p[N];

void solve(int x) {
	puts("YES");
	for (int i=1; i<=n; i++) vis[i]=0;
	for (int y:v[x]) vis[y]=1;
	for (int i=1; i<=n; i++) {
		int sum=0;
		for (int y:v[i]) sum+=vis[y];
		if (sum && sum!=v[x].size() && sum!=v[i].size()) return printf("%d %d\n", i, x), void();
	}
}

int find(int x) {
	return fa[x]==x ? x : fa[x]=find(fa[x]);
}

int main(){
	// fre(discuss);
	int T=read();
	while (T--) {
		n=read();
		for (int i=1; i<=n; i++) {
			int m=read();
			p[m].push_back(i);
			while (m--) v[i].push_back(read());
		}
		for (int i=1; i<=n; i++) fa[i]=i, sz[i]=1;
		for (int i=1; i<=n; i++) {
			for (int j:p[i]) {
				int sum=0;
				for (int x:v[j]) {
					int tx=find(x);
					if (!vis[tx]) vis[tx]=1, sum+=sz[tx];
				}
				if (sum>v[j].size()) {solve(j); goto finish;}
				for (int x:v[j]) {
					int tx=find(x);
					vis[tx]=0;
				}
				int rt=find(v[j][0]);
				for (int x:v[j]) {
					int tx=find(x);
					if (tx!=rt) fa[tx]=rt, sz[rt]+=sz[tx];
				}
			}
		}
		puts("NO"); finish:;
		for (int i=1; i<=n; i++) vis[i]=0, v[i].clear(), p[i].clear();
	}
	return 0;
}
```

### Sol#2

还有一种线性的做法。

我们考虑按集合大小从大到小插入每一个集合。

每个点开始属于编号为 $0$ 的集合。

每次插入一个集合，判断这个集合中每一个元素所属之前的哪些集合。

如果超过一个集合，则这个元素一定可以和那些集合中的任意一个满足题目的条件，暴力找到答案输出即可。

否则当前集合一定包含于之前的某个集合，我们把当前集合的每个元素所属的集合修改为当前集合的编号，集合个数会增加，一定会比不修改更优。

时间复杂度：$O(m)$

#### CODE

```cpp
#include <cstdio>
#include <cctype>
#include <vector>
#define fre(x) freopen(#x".in", "r", stdin); freopen(#x".out", "w", stdout)

using namespace std;

int read() {
	int x=0, fa=0; char c=getchar();
	while (!isdigit(c)) fa|=c=='-', c=getchar();
	while (isdigit(c)) x=(x<<3)+(x<<1)+(c^48), c=getchar();
	return fa ? -x : x;
}

const int N=1e6+10;
int n, vis[N], belong[N];
vector<int> v[N], p[N];

void solve(int x) {
	puts("YES");
	for (int i=1; i<=n; i++) vis[i]=0;
	for (int y:v[x]) vis[y]=1;
	for (int i=1; i<=n; i++) {
		int sum=0;
		for (int y:v[i]) sum+=vis[y];
		if (sum && sum!=v[x].size() && sum!=v[i].size()) return printf("%d %d\n", i, x), void();
	}
}

int main(){
	// fre(discuss);
	int T=read();
	while (T--) {
		n=read();
		for (int i=1; i<=n; i++) {
			int m=read();
			p[m].push_back(i);
			while (m--) v[i].push_back(read());
		}
		for (int i=n; i>=1; i--) {
			for (int j:p[i]) {
				int tot=0;
				for (int k:v[j]) tot+=!vis[belong[k]], vis[belong[k]]=1;
				if (tot>1) {solve(j); goto finish;}
				for (int k:v[j]) vis[belong[k]]=0, belong[k]=j;
			}
		}
		puts("NO"); finish:;
		for (int i=1; i<=n; i++) vis[i]=0, v[i].clear(), p[i].clear(), belong[i]=0;
	}
	return 0;
}
```

##  T3 如何正确地排序

> 有一个 $m\times n$ 的数组 $a_{i,j}$。
>
>  定义： $$f(i,j)=\min\limits_{k=1}^m(a_{k,i}+a_{k,j})+\max\limits_{k=1}^m(a_{k,i}+a_{k,j})$$ 
>
> 你需要求出 $\sum\limits_{i=1}^n\sum\limits_{j=1}^nf(i,j)$。

### Sol#0

有 $10$ 分是 $n\le 3000$ 的，直接按题目要求暴力即可。

还有 $10$ 分是 $m=2$ 的，我们发现每个数要么在最小值中要么在最大值中，答案即为 $2\times n\times\sum_{i=1}^n a_{1,i}+a_{2,i}$

### Sol#1

首先我们可以很容易观察到 $m\le 4$ ，特别特别小！

我们假设 $m=4$ ，若 $m\ne 4$ ，我们在下面补几排 $0$，显然他们不对对答案做出任何贡献。

我们先考虑 $\max\limits_{k=1}^m(a_{k,i}+a_{k,j})$ 怎么求，对于每一个 $f(i,j)$ 只有四种取值：$a_{1,i}+a_{1,j},a_{2,i}+a_{2,j},a_{3,i}+a_{3,j},a_{4,i}+a_{4,j}$

我们需要求出 $a_{k,i}$ 会对答案贡献多少次。

我们假设 $\\{A,B,C,D\\}=\\{1,2,3,4\\}$

$a_{A,i}$ 对答案做出贡献需要满足： $a_{A,i}+a_{A,j}>a_{B,i}+a_{B,j}$，则 $a_{A,i}-a_{B,i}>a_{B,j}-a_{A,j}$

对于 $C$、$D$ 也同理：$a_{A,i}-a_{C,i}>a_{C,j}-a_{A,j}$，$a_{A,i}-a_{D,i}>a_{D,j}-a_{A,j}$

对于每一个 $i$，求有多少个 $j$ 满足条件，这显然是一个三位数点，用 CDQ 分治求三位偏序即可。

再考虑 $\min$ 怎么求，我们把每个数变成相反数，则求 $\min$ 相当于求 $\max$，为了防止溢出，在加上一个大数，最后在减掉即可。

时间复杂度：$O(n\log n)$

#### CODE

```cpp
#include <cstdio>
#include <cctype>
#include <algorithm>
#define int long long
#define fre(x) freopen(#x".in", "r", stdin); freopen(#x".out", "w", stdout)

using namespace std;

int read() {
	int x=0, f=0; char c=getchar();
	while (!isdigit(c)) f|=c=='-', c=getchar();
	while (isdigit(c)) x=(x<<3)+(x<<1)+(c^48), c=getchar();
	return f ? -x : x;
}

const int N=2e5+10, M=N+N;
int n, m, res, a[5][N], b[N], v[M][3], id[M], c[M], t[M];

void add(int x){x+=N;while(x<M){c[x]++;x+=x&-x;}}
int ask(int x){int r=0; x+=N;while(x){r+=c[x];x-=x&-x;}return r;}
void clr(int x){x+=N;while (x<M){c[x]=0;x+=x&-x;}}

void cdq(int l, int r) {
	if (l==r) return;
	int mid=l+r>>1, p1=l, p2=mid+1, m=0;
	cdq(l, mid); cdq(mid+1, r);
	for (int i=l; i<=r; i++)
		if (p1>mid || p2<=r && v[id[p1]][1]>v[id[p2]][1]) {
			if (id[p2]>n) res+=b[id[p2]-n]*ask(v[id[p2]][2]);
			t[i]=id[p2++];
		}
		else {
			if (id[p1]<=n) add(v[id[p1]][2]);
			t[i]=id[p1++];
		}
	for (int i=l; i<=mid; i++)
		if (id[i]<=n) clr(v[id[i]][2]);
	for (int i=l; i<=r; i++) id[i]=t[i];
}

bool cmp(int x, int y) {return v[x][0]==v[y][0] ? x<y : v[x][0]<v[y][0];}

int solve() {
	res=0;
	for (int i=1; i<=4; i++) {
		for (int j=1; j<=n; j++) {
			int cnt=0; b[j]=a[i][j];
			for (int k=1; k<=4; k++) {
				if (k==i) continue;
				if (k<i) v[j+n][cnt]=a[i][j]-a[k][j]-1;
				else v[j+n][cnt]=a[i][j]-a[k][j];
				v[j][cnt++]=a[k][j]-a[i][j];
			}
		}
		for (int j=1; j<=n*2; j++) id[j]=j;
		sort(id+1, id+n*2+1, cmp);
		cdq(1, n*2);
	}
	return res;
}

signed main() {
	// fre(sort);
	m=read(), n=read();
	for (int i=1; i<=m; i++)
		for (int j=1; j<=n; j++)
			a[i][j]=read();
	int ans=solve();
	for (int i=1; i<=m; i++)
		for (int j=1; j<=n; j++)
			a[i][j]=200000-a[i][j];
	ans-=solve();
	printf("%lld\n", 2*(200000*n*n+ans));
	return 0;
}
```

#### Sol#2

一种可能更简单的方法。

我们考虑当 $n=3$ 时求 $\max$ ，就相当于二维数点，求 $\min$ 类似。

当 $n=4$ 时，答案等于

$$
\\begin{equation}\\begin{aligned}ANS&=\\max(a,b,c,d)+\\min(a,b,c,d)\\\\&=\\max(a,b,c)+\\max(a,b,d)+\\max(a,c,d)+\\max(b,c,d)\\\\&-\\max(a,b)-\\max(a,c)-\\max(a,d)-\\max(b,c)-\\max(b,d)-\\max(c,d)\\\\&+a+b+c+d\\end{aligned}\\end{equation}
$$

就不用求那么麻烦的三维偏序了，只要求二位偏序即可。

代码简单了好多。

时间复杂度：$O(n\log n)$

#### CODE

```
懒得写了
```

## 后记

虽然这次 NOI Online 的题目不难，但是我太菜了，考场上后面两题都没有想到qwq
