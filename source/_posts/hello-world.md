---
title: Hello World
date: 2022-03-29 19:12:04
updated: 2022-03-29 19:12:04
comments: true
---
 给定一张 $ N $ 个点（编号 $ 1,2…N $），$ M $ 条边的有向图，求从起点 $ S $ 到终点 $ T $ 的第 $ K $ 短路的长度，路径允许重复经过点或边。

**注意：** 每条最短路中至少要包含一条边。

#### 输入格式

第一行包含两个整数 $ N $ 和 $ M $。

接下来 $ M $ 行，每行包含三个整数 $ A,B $ 和 $ L $，表示点 $ A $ 与点 $ B $ 之间存在有向边，且边长为 $ L $。

最后一行包含三个整数 $ S,T $ 和 $ K $，分别表示起点 $ S $，终点 $ T $ 和第 $ K $ 短路。

#### 输出格式

输出占一行，包含一个整数，表示第 $ K $ 短路的长度，如果第 $ K $ 短路不存在，则输出 $ -1 $。

#### 数据范围

$ 1 \le S,T \le N \le 1000 $,  
$ 0 \le M \le 10^4 $,  
$ 1 \le K \le 1000 $,  
$ 1 \le L \le 100 $

#### 输入样例：

```
2 2
1 2 5
2 1 4
1 2 2
```

#### 输出样例：

```
14
``` 


$\frac{1}{2}\times2=2$

$$
\sum_{i-1}^{n}i=\frac{i\times(i-1)}{2}
$$

## 代码高亮测试

```cpp
#include <cstdio>
#include <cctype>
#include <queue>
#include <cstring>
#include <cmath>
#define fre(x) freopen(#x".in", "r", stdin); freopen(#x".out", "w", stdout)

using namespace std;

int read() {
	int x=0, f=0; char c=getchar();
	while (!isdigit(c)) f|=c=='-', c=getchar();
	while (isdigit(c)) x=(x<<3)+(x<<1)+(c^48), c=getchar();
	return f ? -x : x;
}

const int dx[4]={0, 1, 0, -1};
const int dy[4]={1, 0, -1, 0};
const int N=805;
struct Point{int x, y, s;}ghost[2];
int n, m, g[N][N], st[N][N];

int sub(int a, int b) {
	return abs(a-b);
}

bool check(int x, int y, int t) {
	for (int i=0; i<2; i++) 
		if (sub(ghost[i].x, x)+sub(ghost[i].y, y)<=t*2) return 0;
	return 1;
}

bool BFS(queue<Point> &q, int t, int v) {
	while (q.size() && q.front().s<t*v) {
		Point u=q.front(); q.pop();
		if (!check(u.x, u.y, t)) continue;
		for (int i=0; i<4; i++) {
			int a=u.x+dx[i], b=u.y+dy[i];
			if (~a && ~b && a<n && b<m && g[a][b] && check(a, b, t)) {
				if (!st[a][b]) {
					st[a][b]=st[u.x][u.y];
					q.push({a, b, u.s+1});
				}
				else if (st[a][b]!=st[u.x][u.y]) return 1;
			}
		}
	}
	return 0;
}

int bfs(Point boy, Point girl) {
	memset(st, 0, sizeof st);
	queue<Point> q_boy, q_girl;
	q_boy.push(boy), q_girl.push(girl);
	st[boy.x][boy.y]=1, st[girl.x][girl.y]=2;
	for (int t=1; q_boy.size() || q_girl.size(); t++)
		if (BFS(q_boy, t, 3) || BFS(q_girl, t, 1)) return t;
	return -1;
}

int main() {
	int T=read();
	while (T--) {
		n=read(), m=read();
		Point boy, girl;
		for (int i=0, cnt=0; i<n; i++, getchar())
			for (int j=0; j<m; j++) {
				char ch=getchar();
				if (ch=='M') boy={i, j, 0};
				else if (ch=='G') girl={i, j, 0};
				else if (ch=='Z') ghost[cnt++]={i, j, 0};
				g[i][j]=ch!='X';
			}
		printf("%d\n", bfs(boy, girl));
    }
    return 0;
}
```