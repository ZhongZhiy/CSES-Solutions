动态区间反转和区间求和, 使用 FHQ_Treap 维护序列, 区间反转就实现一个`rev`函数, 把`[l, r]`这段分裂出来, 打上懒标记

区间求和类似线段树, 在`push_up`里实现

```cpp

#include <iostream>
#include <random>
#define int long long
using namespace std;

// 引入高强度随机数生成器
mt19937 rng(114514);

constexpr int N = 2e5 + 10;

struct FHQ_Treap {
    int l[N], r[N], val[N], priority[N], sz[N], sum[N], lazy[N];
    int root, node_cnt;

    // 清空 / 初始化
    void init() {
        root = node_cnt = 0;
    }

    // 向上更新子树大小
    void push_up(int u) {
        sz[u] = sz[l[u]] + sz[r[u]] + 1;
        sum[u] = sum[l[u]] + sum[r[u]] + val[u];
    }

    void push_down(int u){
        if(!lazy[u] || !u) return;
        swap(l[u], r[u]);
        if(l[u]) lazy[l[u]] ^= 1;
        if(r[u]) lazy[r[u]] ^= 1;
        lazy[u] = 0;
    }

    // 新建一个节点
    int create_node(int v) {
        node_cnt++;
        l[node_cnt] = r[node_cnt] = 0;
        val[node_cnt] = v;
        priority[node_cnt] = rng(); // 赋予随机优先级
        sz[node_cnt] = 1;
        sum[node_cnt] = v;
        return node_cnt;
    }

    void split(int u, int v, int &x, int &y) {
        if (!u) {
            x = y = 0;
            return;
        }
        push_down(u);
        int left_size = sz[l[u]];
        if (left_size + 1 <= v) {
            x = u; // 当前节点属于左树
            split(r[u], v - left_size - 1, r[u], y); // 递归去分裂它的右子树
            push_up(x);
        } else {
            y = u; // 当前节点属于右树
            split(l[u], v, x, l[u]); // 递归去分裂它的左子树
            push_up(y);
        }
    }

    // 核心操作：合并树 x 和树 y, x 放在 y的左边
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
        root = merge(root, create_node(v));
    }

    void rev(int l, int r){
        int x, y, z;
        split(root, r, x, z);
        split(x, l-1, x, y);
        lazy[y] ^= 1;
        root = merge(merge(x, y), z);
    }

    int get_sum(int l, int r){
        int x, y, z;
        split(root, r, x, z);
        split(x, l-1, x, y);
        int res = sum[y];
        root = merge(merge(x, y), z);
        return res;
    }

} treap;


signed main(){
    ios::sync_with_stdio(0);cin.tie(0); cout.tie(0);
    int n, m; cin >> n >> m;
    treap.init();
    for(int i= 1; i <= n; ++i){
        int x; cin >> x;
        treap.insert(x);
    }
    for(int i= 1;i <= m; ++i){
        int op, l, r; cin >> op >> l >> r;
        if(op == 1) treap.rev(l, r);
        else cout << treap.get_sum(l, r) << endl;
    }
}

```
