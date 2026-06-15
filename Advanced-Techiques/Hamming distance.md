## 题解
暴力跑 $O(n^2)$ 是可以接受的, 但是必须使用`__builtin_popcount(unsize)`内置函数计算 $1$ 的个数才不会超时

## 参考代码
```cpp
#include <climits>
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
// #define int long long
typedef vector<int> vi;
#define F first
#define S second
typedef pair<int, int> pii;
typedef vector<vector<int>> vvi;
typedef long long ll;
 
const int N = 2e4 + 10;
int a[N];
 
inline int popcount(int x){
    int cnt = 0;
    while(x > 0) {
        x &= (x - 1);
        cnt++;
    }
    return cnt;
}
 
signed main() {
    hello;
    int n, k; cin >> n >> k;
    vector<int> v(n+1);
    fi(n) {
        string s; cin >> s;
        int val = 0;
        for(auto c : s) {
            val = (val << 1 | c - '0');
        }
        v[i] = val;
    }
    int dis = INT_MAX;
    fi(n) {
        if(dis == 0) break;
        for(int j = i + 1; j <= n; ++j) {
        dis = min(dis, __builtin_popcount(v[i] ^ v[j]));
        }
    }
    cout << dis << endl;
 
    world;
}
```
