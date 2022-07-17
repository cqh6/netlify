---
title: CF1238G Adilbek and the Watering System 题解
date: 2022-07-17 16:29:37
updated: 2022-07-17 16:29:37
tags:
  - 贪心
categories:
  - 题解
comments: true
---
### [题目链接](https://www.luogu.com.cn/problem/CF1238G)

### 题意

![](https://cdn.luogu.com.cn/upload/image_hosting/fut4r4fs.png)

贪心QwQ

按时间排序，每次取最便宜的在等待的时候用。

若超出了取出最贵的即可。

显然 `map` 可以直接维护

### CODE

```cpp
#include <bits/stdc++.h>
#define int long long

using namespace std;

const int N=5e5+10;

int read() {
	int x=0, f=0; char c=getchar();
	while (!isdigit(c)) f|=c=='-', c=getchar();
	while (isdigit(c)) x=(x<<3)+(x<<1)+(c^48), c=getchar();
	return f ? -x : x;
}

int n, m, c, c0, ans;
map<int, int> s;
struct water{int t, a, b;} a[N];

signed main() {
	int T=read();
	while (T--) {
		n=read(), m=read(), c=read(), c0=read();
		for (int i=1; i<=n; i++) a[i].t=read(), a[i].a=read(), a[i].b=read();
		sort(a+1, a+n+1, [](water a, water b){return a.t<b.t;});
		s[0]=c0, a[0].t=0, a[n+1].t=m, ans=0;
		int flag=1, sum=c0;
		for (int i=1; i<=n+1; i++) {
			auto it=s.begin();
			int cnt=a[i].t-a[i-1].t;
			sum-=cnt;
			if (sum<0) {flag=0; break;}
			while (cnt && s.size()) {
				if (it->second>cnt) ans+=cnt*it->first, it->second-=cnt, cnt=0;
				else ans+=it->first*it->second, cnt-=it->second, s.erase(it), it=s.begin();
			}
			sum+=a[i].a; s[a[i].b]+=a[i].a;
			it=s.end(); --it;
			while (sum>c) {
				if (it->second>sum-c) it->second-=sum-c, sum=c;
				else sum-=it->second, s.erase(it), it=s.end(), it--;
			}
		}
		if (flag) printf("%lld\n", ans);
		else puts("-1"); s.clear();
	}
	return 0;
}
```