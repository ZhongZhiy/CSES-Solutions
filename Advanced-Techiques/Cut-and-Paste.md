
这是一个区间操作(动态维护序列、涉及区间的翻转、裁剪、粘贴、插入、删除)的题, 要求区间剪切和合并, 其实就是用 `FHQ_Treap`的模板, 保持区间中序遍历顺序不变的同时分裂和合并, 只不过分裂的时候不是按照值分裂, 而是按照 `size`, 也就是排名分裂

当需要操作区间`[3, 5]` 的时候, 先分裂出`[1, 5], [6, n]`, 然后再分裂 `[1, 2], [3, 5]`, 再依次合并就可以了, 最后中序遍历打印答案
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <random>

using namespace std;

// 引入更高效的随机数生成器，确保 Treap 的优先级随机，从而维持平衡
mt19937 rng(20260615);

const int MAXN = 200005;

// FHQ Treap 节点结构体
struct Node {
    int l, r;       // 左右子节点编号
    int size;       // 当前子树的节点总数
    int priority;   // 堆属性的随机优先级
    char val;       // 当前节点存储的字符
} tree[MAXN];

int root, node_cnt;

// 创建一个新节点
int create_node(char ch) {
    node_cnt++;
    tree[node_cnt].l = tree[node_cnt].r = 0;
    tree[node_cnt].size = 1;
    tree[node_cnt].priority = rng();
    tree[node_cnt].val = ch;
    return node_cnt;
}

// 向上更新子树的 size
void push_up(int u) {
    if (u) {
        tree[u].size = tree[tree[u].l].size + tree[tree[u].r].size + 1;
    }
}

// 分裂操作：将以 u 为根的树，按照隐式键值（大小前 k 个）拆分成两棵树 x 和 y
void split(int u, int k, int& x, int& y) {
    if (!u) {
        x = y = 0;
        return;
    }
    // 计算左子树的实际大小
    int left_size = tree[tree[u].l].size;

    if (left_size + 1 <= k) {
        // 说明第 k 个节点在右子树，当前节点及其左子树都属于拆开后的左边（x）
        x = u;
        split(tree[u].r, k - left_size - 1, tree[u].r, y);
        push_up(x);
    } else {
        // 说明第 k 个节点在左子树，当前节点及其右子树都属于拆开后的右边（y）
        y = u;
        split(tree[u].l, k, x, tree[u].l);
        push_up(y);
    }
}

// 合并操作：将树 x 和树 y 按照先后顺序合并，根据随机优先级决定谁做父亲
int merge(int x, int y) {
    if (!x || !y) return x + y;

    if (tree[x].priority < tree[y].priority) {
        // x 的优先级小，x 做父亲，y 合并到 x 的右子树
        tree[x].r = merge(tree[x].r, y);
        push_up(x);
        return x;
    } else {
        // y 的优先级小，y 做父亲，x 合并到 y 的左子树
        tree[y].l = merge(x, tree[y].l);
        push_up(y);
        return y;
    }
}

// 中序遍历输出最终恢复出的字符串
void output(int u) {
    if (!u) return;
    output(tree[u].l);
    cout << tree[u].val;
    output(tree[u].r);
}

void solve() {
    int n, m;
    if (!(cin >> n >> m)) return;

    string s;
    cin >> s;

    // 1. 将初始字符串里的每个字符依次合并进 Treap
    root = 0;
    node_cnt = 0;
    for (char ch : s) {
        root = merge(root, create_node(ch));
    }

    // 2. 处理 m 次 Cut & Paste 操作
    while (m--) {
        int a, b;
        cin >> a >> b;

        int x, y, z;
        // 先把整棵树按照前 b 个切开，右边得到 z (即 b+1 到末尾)
        split(root, b, x, z);
        // 再把左边的树 x 按照前 a-1 个切开，得到 x (1 到 a-1k) 和 y (a 到 b)
        split(x, a - 1, x, y);

        // 此时：x 是前缀，y 是中间切出来的子串，z 是后缀
        // 按照要求重新拼装：先将前缀和后缀融合成新树，再把 y 粘在最末尾
        root = merge(x, z);
        root = merge(root, y);
    }

    // 3. 输出结果
    output(root);
    cout << "\n";
}

int main() {
    // 极致优化输入输出
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);

    solve();

    return 0;
}
```
