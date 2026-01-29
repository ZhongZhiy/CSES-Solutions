---
title: CSES Meet in the Middle
date: 2026-01-29
categories: 
tags:
  - 算法
  - 技巧
status:

link: https://cses.fi/problemset/task/1628
---

# CSES Meet in the Middle
## 题意

> [!abstract] 题目关键信息
>给定 $n$ 个数, 求和为 $m$ 的子序列个数

##  题解
求子序列和为 $x$ 有 $3$ 种方法, 
1. 和不大的时候使用01背包[[DP]]
2.  $n\leq 40$ 的时候用折半搜索
	1. 把 $n$ 分成两份 $len_{1}, len_{2}$
	2. 用状态压缩暴力计算 $len_{1}, len_{2}$ 的所有情况
	3. 对 $L_{2}$ 排序
	4. 对 $L_{1}$ 中每个数字使用二分查找 $l_{2}$ 中 $m-L_{1}[i]$ 的个数
3. 生成函数(不会)

##  参考代码
> [!example] 
```cpp
#include <algorithm>
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
#define all(x) (x).begin(), (x).end()
#define hello ios::sync_with_stdio(0); cin.tie(0);cout.tie(0);
#define world return 0;
#define int long long
typedef vector<int> vi;
#define F first
#define S second
typedef pair<int, int> pii;
typedef vector<vector<int>> vvi;
typedef long long ll;

const int N = 2e6;
int a1[45], a2[45];
int l1[N], l2[N];

void cal_all(int * a, int len, int *res) {
    for(int i = 0;i < (1 << len); ++i) {
        for(int j = 0; j <= len; ++j) {
            if(i>>j & 1) res[i] += a[j+1];
        }
    }
}

signed main() {
    hello;
    int n, m; cin >> n >> m;
    int len1 = n/2, len2 = n - n / 2;
    fi(len1) cin >> a1[i];
    fi(len2) cin >> a2[i];
    cal_all(a1, len1, l1);
    cal_all(a2, len2, l2);

    sort(l2, l2 + (1<<len2));
    int ans = 0;
    for(int i = 1; i <= (1 << len1); ++i) {
        auto cnt = upper_bound(l2, l2+(1<<len2), m - l1[i]) - lower_bound(l2, l2+(1<<len2), m - l1[i]);
        ans += cnt;
    }
    cout << ans << endl;


    world;
}


```
