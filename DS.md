



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

###  Binary Lifting & LCA
```
struct BL {  
// 1 based  
    vector<int> depth;  
    vector<vector<int>> edges;  
    vector<vector<int>> up;  
    const int Log = 22;  
  
    BL(int n) {  
        depth = vector<int>(n + 10);  
        edges = vector<vector<int>>(n + 10);  
        up = vector<vector<int>>(n + 10, vector<int>(Log));  
    }  
  
    void dfs(int node, int parent, int cnt) {  
        depth[node] = cnt;  
        for (auto it: edges[node]) {  
            if (it == parent)continue;  
            up[it][0] = node;  
            for (int j = 1; j < Log; j++)  
                up[it][j] = up[up[it][j - 1]][j - 1];  
            dfs(it, node, cnt + 1);  
        }  
    }  
  
    int get_ancestor(int x, int k) {  
        if (k > depth[x])return -1;  
        for (int j = Log - 1; j >= 0; j--) {  
            if (k & (1 << j)) {  
                x = up[x][j];  
            }  
        }  
        return x;  
    }  
  
    int get_LCA(int a, int b) {  
        if (depth[a] < depth[b])swap(a, b);  
        int k = depth[a] - depth[b];  
        for (int j = Log - 1; j >= 0; j--) {  
            if (k & (1 << j)) {  
                a = up[a][j];  
            }  
        }  
        if (a == b)return a;  
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
struct Matrix
{
    vector<vector<int>>a;

    Matrix()
    {
        a = vector<vector<int>>(sz, vector<int>(sz));
    }

    Matrix operator*(Matrix &other)
    {
        Matrix ans;

        for (int i = 0; i < sz; ++i)
        {
            for (int j = 0; j < sz; ++j)
            {
                for (int k = 0; k < sz; ++k)
                {
                    ans.a[i][j] +=  (a[i][k] * other.a[k][j]);
                    ans.a[i][j] %= MOD;
                }
            }
        }
        return ans;
    }
};

Matrix power(Matrix x, unsigned int y)
{
    Matrix res;


    for (int i = 0; i < sz; ++i)
    {
        res.a[i][i] = 1;
    }



    while (y > 0)
    {
        if (y & 1)
            res = (res * x);
        y = y >> 1;
        x = (x * x);
    }
    return res;
}

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