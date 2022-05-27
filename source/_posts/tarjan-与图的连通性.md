---
index_img: ""
title: Tarjan 与图的连通性
date: 2022-05-09 21:29:49
updated: 2022-05-27 19:22:28
tags:
  - tarjan
  - 缩点
  - 图论
categories:
  - 算法
comments: true
---
注：链式前向星从2开始编号，缩点后的新边也从2开始

因为我太逊~~（懒）~~了，所以只有代码qwq

## 模板题链接

1. [dcc 割边](https://www.luogu.com.cn/problem/T103481)
2. [dcc 割点](https://www.luogu.com.cn/problem/P3388)
3. [e-dcc](https://www.luogu.com.cn/problem/T103489)
4. [v-dcc](https://www.luogu.com.cn/problem/T103492)
5. [scc](https://www.luogu.com.cn/problem/B3609)
6. [scc 缩点](https://www.luogu.com.cn/problem/P3387)

没有贺到 e-dcc 和 v-dcc 缩点的模板题qwq

## tarjan 与无向图连通性

### tarjan 求割边（桥）

```cpp
void tarjan(int x, int id) {
    dfn[x]=low[x]=++num;
    for (int i=head[x]; i; i=nxt[i]) {
        int y=ver[i];
        if (!dfn[y]) {
            tarjan(y, i);
            low[x]=min(low[x], low[y]);
            if (low[y]>dfn[x]) bridge[i]=bridge[i^1]=1;
        }
        else if (i!=(id^1)) low[x]=min(low[x], dfn[y]);
    }
}
```

### tarjan 求割点

```cpp
void tarjan(int x) {
    dfn[x]=low[x]=++num;
    int flag=0;
    for (int i=head[x]; i; i=nxt[i]) {
        int y=ver[i];
        if (!dfn[y]) {
            tarjan(y);
            low[x]=min(low[x], low[y]);
            if (low[y]>=dfn[x] && (x!=rt || (++flag)>1)) cut[x]=1;
        }
        else low[x]=min(low[x], dfn[y]);
    }
}
```

### tarjan e-DCC 求边双+缩点

```cpp
void dfs(int x) {
    c[x]=dcc;
    for (int i=head[x]; i; i=nxt[i]) {
        int y=ver[i];
        if (c[y] || bridge[i]) continue;
        dfs(y);
    }
}

for (int i=1; i<=n; i++)
    if (!c[i]) { dcc++; dfs(i); }
for (int i=2; i<=tot; i++) {
    int x=ver[i], y=ver[i^1];
    if (c[x]!=c[y]) add_c(c[x], c[y]);
}
```

### tarjan v-DCC 求点双+缩点

```cpp
void tarjan(int x) {
    dfn[x]=low[x]=++num;
    s.push(x);
    if (x==rt && head[x]==0) return dcc[++cnt].push_back(x), void();
    int flag=0;
    for (int i=head[x]; i; i=nxt[i]) {
        int y=ver[i];
        if (!dfn[y]) {
            tarjan(y);
            low[x]=min(low[x], low[y]);
            if (low[y]>=dfn[x]) {
                if (x!=rt || (++flag)>1) cut[x]=1;
                int z; cnt++;
                do {
                    z=s.top(); s.pop();
                    dcc[cnt].push_back(z);
                } while (z!=y);
                dcc[cnt].push_back(x);
            }
        }
        else low[x]=min(low[x], dfn[y]);
    }
}

num=cnt;
for (int i=1; i<=n; i++)
    if (cut[i]) new_id[i]=++num;
for (int i=1; i<=cnt; i++)
    for (int x:dcc[i])
        if (cut[x]) add_c(i, new_id[x]), add_c(new_id[x], i);
        else c[x]=i;
```

## tarjan 与有向图连通性

### tarjan 求强连通分量+缩点

```cpp
void tarjan(int x) {
    dfn[x]=low[x]=++num;
    s.push(x); ins[x]=1;
    for (int i=head[x]; i; i=nxt[i]) {
        int y=ver[i];
        if (!dfn[y]) {
            tarjan(y);
            low[x]=min(low[x], low[y]);
        }
        else if (ins[y]) low[x]=min(low[x], dfn[y]);
    }
    if (dfn[x]==low[x]) {
        int y; cnt++;
        do {
            y=s.top(); s.pop(); ins[y]=0;
            c[y]=cnt, scc[cnt].push_back(y);
        } while (x!=y);
    }
}

for (int x=1; x<=n; x++)
    for (int j=head[x]; i; i=nxt[i]) {
        int y=ver[i];
        if  (c[x]!=c[y]) add_c(c[x], c[y]);
    }
```