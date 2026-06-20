区间反转, 依然是动态区间修改类型, 使用 FHQ_Treap 可以实现均摊每次修改`log(N)`的时间复杂度, 此外还需要使用 **懒标记** 优化

```cpp
#include <iostream>
#include <locale>
#include <random>

using namespace std;

mt19937 rng(114514);

constexpr int N = 2e5 + 10;

struct FHQ_Treap {
    int l[N], r[N], val[N], priority[N], sz[N], lazy[N];
    int root, node_cnt;

    // 清空 / 初始化
    void init() {
        root = node_cnt = 0;
    }

    // 向上更新子树大小
    void push_up(int u) {
        sz[u] = sz[l[u]] + sz[r[u]] + 1;
    }

    //传递懒标记
    void push_down(int u){
        if(!u || !lazy[u]) return;
        lazy[u] ^= 1;
        swap(l[u], r[u]);
        lazy[l[u]]^=1;
        lazy[r[u]]^=1;
    }

    // 新建一个节点
    int create_node(int v) {
        node_cnt++;
        l[node_cnt] = r[node_cnt] = 0;
        val[node_cnt] = v;
        priority[node_cnt] = rng(); // 赋予随机优先级
        sz[node_cnt] = 1;
        return node_cnt;
    }


    void split(int u, int size, int &x, int &y) {
        if (!u) {
            x = y = 0;
            return;
        }
        push_down(u);
        int lcnt = sz[l[u]];
        if (lcnt+1 <= size) {
            x = u; // 当前节点属于左树
            split(r[u], size-lcnt-1, r[u], y); // 递归去分裂它的右子树
            push_up(x);
        } else {
            y = u; // 当前节点属于右树
            split(l[u], size, x, l[u]); // 递归去分裂它的左子树
            push_up(y);
        }
    }

    // 核心操作：合并树 x 和树 y
    int merge(int x, int y) {
        if (!x || !y) return x + y;

        // 依据随机优先级，决定谁做父亲（这里以小根堆为例）
        if (priority[x] < priority[y]) {
            push_down(x);
            r[x] = merge(r[x], y); // x 做父亲，y 去和 x 的右子树合并
            push_up(x);
            return x;
        } else {
            push_down(y);
            l[y] = merge(x, l[y]); // y 做父亲，x 去和 y 的左子树合并
            push_up(y);
            return y;
        }
    }

    // 高级外挂：插入一个值 v
    void insert(int v) {
        //因为字符串本身有序, 所以直接插入到右边就可以了
        root = merge(root, create_node(v));
    }
    void reverse(int l, int r){
        int x , y, z;
        split(root, r, x, z);
        split(x, l-1, x, y);
        lazy[y]^= 1;
        root = merge(x, y);
        root = merge(root, z);
    }
    void print(int id){
        if(!id) return;
        push_down(id);
        print(l[id]);
        cout << (char)val[id];
        print(r[id]);
    }
} treap;

void solve(){
    int n, m; cin >> n >> m;
    string s; cin >> s;
    treap.init();
    for(auto &c : s){
        treap.insert(c);
    }

    for(int i = 1;i <= m; ++i){
        int x, y; cin >> x >> y;
        treap.reverse(x, y);
    }
    treap.print(treap.root);
}

signed main(){
    ios::sync_with_stdio(0);cin.tie(0);cout.tie(0);
    solve();
}
```
