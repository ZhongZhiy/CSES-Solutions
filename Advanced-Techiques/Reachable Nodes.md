---
title: CSES Reachable Nodes
date: 2026-01-29
categories:
tags:
  - 拓扑排序
  - 算法
  - bitset
status:
link: https://cses.fi/problemset/result/16091601/
---

# CSES Reachable Nodes
## 题意

> [!abstract] 题目关键信息
在有向无环图中求每个节点能够到达的节点个数, 


##  题解
每个节点能到达的个数也是它的父节点能到达的个数, 但是如是是 $1\to 2 \to 3 ;1 \to 3$ 那么 $1$ 就会重复计算 $3$ 节点个个数, 所以考虑去重
>用`bitset`每一位代表对应的数能够到达, 那么 $a \to b$ 就可以表示成 $bitse t_{a} |= bitse t_{b}$

##  参考代码
> [!example] 
```cpp
#pragma GCC optimize("Ofast")
#include<bits/stdc++.h>
using namespace std;

#define DEBUG
#ifdef DEBUG
#define de(x) cout << (#x) << "=" << (x) << endl;
#define de2(x, y) cout << (#x) << " " << (#y) << " = " << (x) << " " << (y) << endl;
#else
#define de(x)
#define de2(x, y)
#endif
#define endl '\n'
#define fi(n) for(int i = 1;i <= n; ++i)
#define fi0(n) for(int i = 0;i < n; ++i)
#define fj(n) for(int j = 1;j <= n; ++j)
// #define all(x) (x).begin(), (x).end()
#define hello ios::sync_with_stdio(0); cin.tie(0);cout.tie(0);
#define world return 0;
#define int long long
typedef vector<int> vi;
#define F first
#define S second
typedef pair<int, int> pii;
typedef vector<vector<int>> vvi;
typedef long long ll;

const int N = 5e4 + 10;
vi re[N];
int outd[N];
bitset<N> reach[N];

signed main() {
    hello;
    int n, m; cin >> n >> m;
    fi(m) {
        int f, t; cin >> f >> t;
        outd[f]++;
        //建立反向边
        re[t].push_back(f);
    }

    queue<int> q;
    fi(n) {
        if(!outd[i]) q.push(i);
    }

    while(!q.empty()) {
        int nd = q.front(); q.pop();
        reach[nd].set(nd);
        for(auto e : re[nd]) {
            reach[e] |= reach[nd];
            outd[e]--;
            if(!outd[e]) q.push(e);
        }
    }

    fi(n) cout << reach[i].count()  << ' ';

    world;
}


```
