******



### Segment Tree
```
struct SG {
    int size;
    vector<int> Seg;
    vector<int> lazy;

    int merge(int a, int b) {
        //merge as required
        return a + b;
    }

    void init(int n) {
        size = 1;
        while (size < n) {
            size *= 2;
        }
        Seg.resize(2 * size - 1);
        lazy.resize(2 * size - 1);
    }

    void Build(vector<int> &arr, int pos, int l, int r) {
        if (l == r) {
            if (l < arr.size()) {
                Seg[pos] = arr[l];
            }
            return;
        }
        int mid = l + (r - l) / 2;
        Build(arr, 2 * pos + 1, l, mid);
        Build(arr, 2 * pos + 2, mid + 1, r);
        Seg[pos] = merge(Seg[2 * pos + 1], Seg[2 * pos + 2]);
    }

    void Build(vector<int> &arr) {
        int n = arr.size();
        Build(arr, 0, 0, size - 1);
    }

    void propagation(int pos, int l, int r) {
        if (!lazy[pos])
            return;
        Seg[pos] += (r - l + 1) * lazy[pos];
        if (l != r) {
            lazy[2 * pos + 1] += lazy[pos];
            lazy[2 * pos + 2] += lazy[pos];
        }
        lazy[pos] = 0;
    }

    void set(int st, int en, int val, int pos, int l, int r) {
        propagation(pos, l, r);
        if (l > en || r < st)
            return;
        if (st <= l && r <= en) {
            lazy[pos] += val;
            propagation(pos, l, r);
            return;
        }
        int mid = l + (r - l) / 2;
        set(st, en, val, 2 * pos + 1, l, mid);
        set(st, en, val, 2 * pos + 2, mid + 1, r);
        Seg[pos] = merge(Seg[2 * pos + 1], Seg[2 * pos + 2]);
    }

    void set(int st, int en, int val) {
        set(st, en, val, 0, 0, size - 1);
    }

    int Query(int st, int en, int pos, int l, int r) {
        propagation(pos, l, r);
        if (l > en || r < st) {
            return 0;
        }
        if (st <= l && r <= en) {
            return Seg[pos];
        }
        int mid = l + (r - l) / 2;
        //if there is calls here , call them or call 911 for contest
        int ans = merge(Query(st, en, 2 * pos + 1, l, mid), Query(st, en, 2 * pos + 2, mid + 1, r));
        Seg[pos] = merge(Seg[2 * pos + 1], Seg[2 * pos + 2]);
        return ans;
    }

    int Query(int st, int en) {
        return Query(st, en, 0, 0, size - 1);
    }
};
```



### Sparse Table

```
struct SP {
    vector<int> input;
    vector<vector<int>> Sparse;

    void init(vector<int> &arr, int n) {
        Sparse.resize(n, vector<int>(log2(n) + 1, 0));//store index for 'min'
        input.resize(n);
        for (int i = 0; i < n; i++) {
            Sparse[i][0] = i;
            input[i] = arr[i];
        }
        for (int j = 1; (1 << j) <= n; j++) {
            for (int i = 0; i + (1 << j) - 1 < n; i++) {
                if (arr[Sparse[i][j - 1]] < arr[Sparse[i + (1 << (j - 1))][j - 1]]) {
                    Sparse[i][j] = Sparse[i][j - 1];
                } else {
                    Sparse[i][j] = Sparse[i + (1 << (j - 1))][j - 1];
                }
            }
        }
    }

    int QueryMin(int l, int r) {
        int len = r - l + 1;
        int k = log2(len);
        return min(input[Sparse[l][k]], input[Sparse[l + len - (1 << k)][k]]);
    }

    int query(int l, int r) // o(1)
    {
        int len = r - l + 1;
        int j = 31 - __builtin_clz(len);
        int g = __gcd(GCD[l][j], GCD[r - (1 << j) + 1][j]);
        return g;
    }

};
```

### Max Subarray Sum with Segment tree

```struct item {
    int seg{}, pre{}, suf{}, sum{};
};
 item merge(item a, item b) {
        return {max({a.seg, b.seg, a.suf + b.pre}), max(a.pre, a.sum + b.pre),
                max(b.suf, b.sum + a.suf), a.sum + b.sum};
    }
```

### DSU

```
struct DSU {  
    vector<int> size,parent;  
    vector<long long> sum;  
    int components;  
    DSU(int n) {  
        size.resize(n + 1, 1);  
        sum.resize(n + 1);  
        parent.resize(n * 1);  
        for (int i = 0; i <= n; i++) {  
            parent[i] = i, sum[i] = i;  
        }  
        components = n;  
    }  
  
    int get_parent(int x) {  
        if (x == parent[x])return x;  
        return parent[x] = get_parent(parent[x]);  
    }  
  
    void move(int x, int y) {  
        y = get_parent(y);  
        int p_x = get_parent(x);  
        if (y == p_x)return;  
        size[p_x]--, sum[p_x] -= x, parent[x] = y, size[y]++, sum[y] += x;  
    }  
    void connect(ll x, ll y) {  
        int px = get_parent(x), py = get_parent(y);  
        if (px == py)return;  
        if (size[px] > size[py])swap(px, py);  
        parent[px] = py;  
        size[py] += size[px];  
        sum[py] += sum[px];  
        components--;  
    }  
    bool connected(int x, int y) {  
        int px = get_parent(x), py = get_parent(y);  
        if (px == py)return true;  
        return false;  
    }  
};
```

### DSU With rollbacks
```
struct DSU {  
    vector<int> size, parent;  
    vector<long long> sum;  
  
    struct details {  
        int parent, child;  
        int pSum, cSum, pSize, cSize;  
        int componentsSize;  
    };  
    stack<details> operations;  
    int components;  
    DSU(int n) {  
        size.resize(n + 1, 1);  
        sum.resize(n + 1);  
        parent.resize(n * 1 + 1);  
        for (int i = 0; i <= n; i++) {  
            parent[i] = i, sum[i] = i;  
        }  
        components = n;  
    }  
    int getParentWithRollback(int x) {  
        if (x == parent[x])return x;  
        return getParentWithRollback(parent[x]);  
    }  
    void connectWithRollback(ll x, ll y) {  
        int px = getParentWithRollback(x), py = getParentWithRollback(y);  
        if (px == py) {  
            operations.push(  
                {py, px, sum[py], sum[px], size[py], size[px], components}  
            );  
            return;  
        }  
        if (size[px] > size[py])swap(px, py);  
        operations.push(  
            {py, px, sum[py], sum[px], size[py], size[px], components}  
        );  
        parent[px] = py;  
        size[py] += size[px];  
        sum[py] += sum[px];  
        components--;  
    }  
    bool connectedWithRollBack(int x, int y) {  
        int px = getParentWithRollback(x), py = getParentWithRollback(y);  
        return (px==py);  
    }  
  
    void rollback(int steps) {  
        while (operations.size() && steps--) {  
            auto [p,c,psum,csum,psize,csize,comps] = operations.top();  
            operations.pop();  
            parent[c] = c;  
            sum[p] = psum;  
            sum[c] = csum;  
            size[p] = psize;  
            size[c] = csize;  
            components = comps;  
        }  
    }  
  
    void clear() {  
        while (operations.size()) {  
            auto [p,c,psum,csum,psize,csize,comps] = operations.top();  
            operations.pop();  
            parent[c] = c;  
            sum[p] = psum;  
            sum[c] = csum;  
            size[p] = psize;  
            size[c] = csize;  
            components = comps;  
        }  
    }  
};
```
###  Binary Lifting & LCA
```
const int Log = 22;  
struct BL {  
    vector<int> depth;  
    vector<vector<int>> edges;  
    vector<vector<int>> up;  
  
    BL(int n) {  
        depth = vector<int>(n + 10, 0);  
        edges = vector<vector<int>>(n + 10);  
        up = vector<vector<int>>(n + 10, vector<int>(Log, -1));  
    }  
  
    void dfs(int node, int parent) {  
        up[node][0] = parent;  
        for (int j = 1; j < Log; j++) {  
            if (up[node][j-1] != -1) {  
                up[node][j] = up[up[node][j-1]][j-1];  
            } else {  
                up[node][j] = -1;  
            }  
        }  
        for (auto it : edges[node]) {  
            if (it == parent) continue;  
            depth[it] = depth[node] + 1;  
            dfs(it, node);  
        }  
    }  
  
    int get_LCA(int a, int b) {  
        if (depth[a] < depth[b]) swap(a, b);  
        int k = depth[a] - depth[b];  
        for (int j = Log - 1; j >= 0; j--) {  
            if (k & (1 << j)) {  
                a = up[a][j];  
                if (a == -1) break;  
            }  
        }  
        if (a == b) return a;  
        for (int j = Log - 1; j >= 0; j--) {  
            if (up[a][j] != up[b][j]) {  
                a = up[a][j];  
                b = up[b][j];  
            }  
        }  
        return up[a][0];  
    }  
};
```
### Trie
```
struct Trie {  
    Trie *arr[26] = {nullptr};  
    int lst = 0;  
    int st = 0;  
  
    Trie() {  
        for (int i = 0; i < 26; ++i) {  
            arr[i] = nullptr;  
        }  
        lst = 0;  
        st = 0;  
    }  
  
    ~Trie() {  
        for (int i = 0; i < 26; ++i) {  
            if (arr[i] != nullptr) {  
                delete arr[i];  
                arr[i] = nullptr;  
            }  
        }  
    }  
  
    void insert(const string &word, int idx = 0) {  
        if (idx == word.size()) {  
            lst++;  
            st++;  
            return;  
        }  
        int ch = word[idx] - 'a';  
        if (arr[ch] == nullptr) {  
            arr[ch] = new Trie();  
        }  
        if (idx != 0)st++;  
        arr[ch]->insert(word, idx + 1);  
    }  
  
    bool erase(const string &word, int idx = 0) {  
        if (idx == word.size()) {  
            lst--;  
            st--;  
            return true;  
        }  
        int ch = word[idx] - 'a';  
        if (arr[ch] == nullptr) {  
            return false;  
        }  
        if (!arr[ch]->erase(word, idx + 1)) {  
            return false;  
        }  
        if (idx != 0) st--;  
        if (arr[ch]->st == 0) {  
            delete arr[ch];  
            arr[ch] = nullptr;  
        }  
        return true;  
    }  
};
```
### BIT
```
struct FenwickTree {
    vector<int> bit;
    int n;

    FenwickTree(int n) {
        this->n = n;
        bit.assign(n, 0);
    }

    FenwickTree(vector<int> const &a) : FenwickTree(a.size()) {
        for (size_t i = 0; i < a.size(); i++)
            add(i, a[i]);
    }

    int sum(int r) {
        int ret = 0;
        for (; r >= 0; r = (r & (r + 1)) - 1)
            ret += bit[r];
        return ret;
    }

    int sum(int l, int r) {
        return sum(r) - sum(l - 1);
    }

    void add(int idx, int delta) {
        for (; idx < n; idx = idx | (idx + 1)) {
            bit[idx] = moooood(bit[idx], delta, Mod);
        }
    }
};
```
### Matrix Expo
```
const int MOD = 1e9 + 7;  
   
ui mul(ui x, ui y) {  
    return ((ll) (x) % MOD * (ll) (y) % MOD) % MOD;  
}  
   
ui add(ui x, ui y) {  
    return ((x) % MOD + (y) % MOD) % MOD;  
}  
struct Matrix {  
    vector<vector<ui> > M;  
  
    static vector<vector<ui> > identity(int n, int m) {  
        vector<vector<ui> > id(n, vector<ui>(m, 0));  
        for (int i = 0; i < n && i < m; ++i)  
            id[i][i] = 1;  
        return id;  
    }  
  
    static vector<vector<ui> > Mul(vector<vector<ui> > a, vector<vector<ui> > b) {  
        int n = a.size();  
        int m = b[0].size();  
        int p = a[0].size(); // Must equal b.size()  
        vector<vector<ui> > result(n, vector<ui>(m, 0));  
        for (int i = 0; i < n; ++i)  
            for (int j = 0; j < m; ++j)  
                for (int k = 0; k < p; ++k)  
                    result[i][j] = add(result[i][j], mul(a[i][k], b[k][j]));  
  
        return result;  
    }  
  
    static vector<vector<ui> > Power(vector<vector<ui> > a, int b) {  
        if (b == 0) return identity(a.size(), a[0].size());  
        auto ans = Power(a, b / 2);  
        ans = Mul(ans, ans);  
        if (b & 1)  
            return Mul(a, ans);  
        return ans;  
    }  
  
  
    vector<vector<ui> > MatrixExpo(vector<vector<ui> > matrix, vector<vector<ui> > a, int p) {  
        M = matrix;  
        M = Power(M, p);  
        return Mul(M, a);  
    }  
};

```
###  LIS log(n)
```
vector<int> LIS(vector<int> &v) {
    vector<int> lis(n);

    vector<int> inc;

    for (int i = 0; i < v.size(); ++i) {
        if (!inc.size() || v[i] >= inc.back()) {
            inc.push_back(v[i]);
        } else {
            inc[upper_bound(inc.begin(), inc.end(), v[i]) - inc.begin()] = v[i];
        }

        lis[i] = upper_bound(inc.begin(), inc.end(), v[i]) - inc.begin();
    }

    return lis;
}
```
### o_set
```
#include <ext/pb_ds/assoc_container.hpp>  
#include <ext/pb_ds/tree_policy.hpp>  
using namespace __gnu_pbds;  
#define ordered_set tree<int, null_type,less<int>, rb_tree_tag,tree_order_statistics_node_update>  
//order_of_key (k) : Number of items strictly smaller than k .  
//find_by_order(k) : K-th element in a set (counting from z
```
### merge sort tree
```
struct SG {  
    int size;  
    vector<vector<int>> Seg;  
  
    vector<int> merge(vector<int> &a, vector<int> &b) {  
        //merge as required  
        vector<int> ans;  
        int l = 0, r = 0;  
        while (l < a.size() && r < b.size()) {  
            if (a[l] < b[r]) {  
                ans.push_back(a[l++]);  
            } else {  
                ans.push_back(b[r++]);  
            }  
        }  
        while (l < a.size()) {  
            ans.push_back(a[l++]);  
        }  
        while (r < b.size()) {  
            ans.push_back(b[r++]);  
        }  
        return ans;  
    }  
  
    int mergeAns(int a, int b) {  
        //merge as required  
        return a + b;  
    }  
  
    void init(int n) {  
        size = 1;  
        while (size < n) {  
            size *= 2;  
        }  
        Seg.resize(2 * size - 1);  
    }  
  
    void Build(vector<int> &arr, int pos, int l, int r) {  
        if (l == r) {  
            if (l < arr.size()) {  
                Seg[pos] = {arr[l]};  
            }  
            return;  
        }  
        int mid = l + (r - l) / 2;  
        Build(arr, 2 * pos + 1, l, mid);  
        Build(arr, 2 * pos + 2, mid + 1, r);  
        Seg[pos] = merge(Seg[2 * pos + 1], Seg[2 * pos + 2]);  
    }  
  
    void Build(vector<int> arr) {  
        int n = arr.size();  
        map<ll, ll> last_seen;  
        for (int i = n - 1; i >= 0; i--) {  
            int val = arr[i];  
            if (last_seen.count(arr[i])) {  
  
                arr[i] = last_seen[arr[i]];  
                last_seen[val] = i;  
            } else arr[i] = n, last_seen[val] = i;  
        }  
//        for(auto it:arr)  
//            cout<<it<<" ";  
//        cout<<endl;  
        Build(arr, 0, 0, size - 1);  
    }  
  
    int Query(int st, int en, int val, int pos, int l, int r) {  
        if (l > en || r < st) {  
            return 0;  
        }  
        if (st <= l && r <= en) {  
            return Seg[pos].end() - upper_bound(Seg[pos].begin(), Seg[pos].end(), val);  
        }  
        int mid = l + (r - l) / 2;  
        int ret = mergeAns(Query(st, en, val, 2 * pos + 1, l, mid), Query(st, en, val, 2 * pos + 2, mid + 1, r));  
        return ret;  
    }  
  
    int Query(int st, int en, int val) {  
        return Query(st, en, val, 0, 0, size - 1);  
    }  
};
```

### Treap
```
struct Treap {  
    struct Node {  
        int val, prior, size;  
        bool rev;  
        Node *l, *r;  
        Node(int v) : val(v), prior(rand()), size(1), rev(false), l(nullptr), r(nullptr) {}  
    };  
  
    using pNode = Node*;  
    pNode root = nullptr;  
  
    Treap() : root(nullptr) {}  
  
    int sz(pNode t) { return t ? t->size : 0; }  
  
    void update(pNode t) {  
        if (t) t->size = 1 + sz(t->l) + sz(t->r);  
    }  
  
    void push(pNode t) {  
        if (t && t->rev) {  
            swap(t->l, t->r);  
            if (t->l) t->l->rev ^= 1;  
            if (t->r) t->r->rev ^= 1;  
            t->rev = false;  
        }  
    }  
  
    void split(pNode t, int k, pNode &l, pNode &r) {  
        if (!t) return void(l = r = nullptr);  
        push(t);  
        if (sz(t->l) >= k) {  
            split(t->l, k, l, t->l);  
            r = t;  
        } else {  
            split(t->r, k - sz(t->l) - 1, t->r, r);  
            l = t;  
        }  
        update(t);  
    }  
  
    pNode merge(pNode l, pNode r) {  
        push(l); push(r);  
        if (!l || !r) return l ? l : r;  
        if (l->prior > r->prior) {  
            l->r = merge(l->r, r);  
            update(l);  
            return l;  
        } else {  
            r->l = merge(l, r->l);  
            update(r);  
            return r;  
        }  
    }  
  
    // Inserts a new node with value 'val' at position 'pos'.  
    void insert(int pos, int val) {  
        pNode l, r;  
        split(root, pos, l, r);  
        root = merge(merge(l, new Node(val)), r);  
    }  
  
    // Updates the node at position 'pos' with a new value (if needed).  
    void update_value(int pos, int new_val) {  
        pNode l, mid, r;  
        split(root, pos, l, r);  
        split(r, 1, mid, r);  
        if (mid) mid->val = new_val;  
        root = merge(merge(l, mid), r);  
    }  
  
    // Returns the value at the given index.  
    int query(int pos) {  
        pNode l, mid, r;  
        split(root, pos, l, r);  
        split(r, 1, mid, r);  
        int result = mid ? mid->val : -1; // or throw error if out-of-bounds  
        root = merge(merge(l, mid), r);  
        return result;  
    }  
  
    // Reverses the entire treap.  
    void reverse_all() {  
        if (root) root->rev ^= 1;  
    }  
  
    // Reverses the subrange [l_pos, r_pos] (inclusive)  
    void reverse_range(int l_pos, int r_pos) {  
        pNode l, mid, r;  
        split(root, l_pos, l, r);  
        split(r, r_pos - l_pos + 1, mid, r);  
        if (mid) mid->rev ^= 1;  
        root = merge(merge(l, mid), r);  
    }  
  
    // Moves the last element to the front (cyclic shift).  
    void rotate_last_to_front() {  
        if (sz(root) <= 1) return;  
        pNode l, last;  
        split(root, sz(root) - 1, l, last); // 'last' is the node at the end.  
        root = merge(last, l);  
    }  
};
```