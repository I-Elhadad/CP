### Sack
```
int n;  
cin >> n;  
vector<int> v(n);  
for (int i = 0; i < n; i++)  
    cin >> v[i];  
vector<int> edges[n];  
for (int i = 0; i < n - 1; i++) {  
    int x, y;  
    cin >> x >> y;  
    x--, y--;  
    edges[x].push_back(y), edges[y].push_back(x);  
}  
vector<int> sz(n), big(n), ans(n), sum(n), freq(1e5 + 1);  
int mx = 0;  
// pre process to calc heavy edge and size of every subtree  
function<void(int, int)> pre = [&](int node, int parent) {  
    sz[node] = 1;  
    big[node] = -1;  
    for (auto it: edges[node]) {  
        if (it != parent) {  
            pre(it, node);  
            sz[node] += sz[it];  
            if (big[node] == -1 || sz[it] > sz[big[node]])  
                big[node] = it;  
        }  
    }  
};  
pre(0, -1);  
// update function to implement logic of adding and removing nodes  
function<void(int, int)> update = [&](int col, int d) {  
    if (freq[col] + d > mx) {  
        mx++;  
    } else if (freq[col] == mx && sum[freq[col]] == col)mx--;  
    sum[freq[col]] -= col;  
    freq[col] += d;  
    sum[freq[col]] += col;  
};  
// cointect  subtree  
function<void(int, int, int)> cointect = [&](int node, int parent, int d) {  
    update(v[node], d);  
    for (auto it: edges[node]) {  
        if (it != parent)  
            cointect(it, node, d);  
    }  
};  
function<void(int, int, bool)> dfs = [&](int node, int parent, bool keep) {  
    for (auto it: edges[node]) {  
        if (it != parent && it != big[node])  
            dfs(it, node, false);  
    }  
  
    // add to DS  
    if (big[node] != -1)  
        dfs(big[node], node, true);  
    update(v[node], 1);  
    for (auto it: edges[node]) {  
        if (it != parent && it != big[node])  
            cointect(it, node, 1);  
    }  
    // answer  queries  
    ans[node] = sum[mx];  
    //cout<<node<<" "<<sum[node]<<endl;  
    // Remove form DS    if (!keep)  
        cointect(node, parent, -1);  
};  
dfs(0, -1, true);
```
### Fastpower
```
const int mod = 1e9 + 7;  
int fastPower(int a, int b) {  
    if(b==0) return 1;  
    long long ans = fastPower(a,b/2)%mod;  
    ans=(ans%mod*ans%mod)%mod;  
    if(b&1)  
        return (a*ans)%mod;  
    return ans;  
}
```
###  Floyed
```
for (int k = 0; k < n; k++) {  
        // Pick all vertices as source one by one  
        for (int i = 0; i < n; i++) {  
            // Pick all vertices as destination for the  
            // above picked source            for (int j = 0; j < n; j++) {  
                // If vertex k is on the shortest path from  
                // i to j, then update the value of            // dist[i][j]                if (dist[i][j] > (dist[i][k] + dist[k][j])  
                    && (dist[k][j] != 1e18  
                        && dist[i][k] != 1e18))  
                    dist[i][j] = dist[i][k] + dist[k][j];  
            }  
        }  
    }
```
### suffix array
```
struct suffixArray {  
private:  
    string s;  
    // pos: pos[i] = starting index of the i-th smallest suffix in 's'.
    vector<int> pos;  
    // cost: cost[i] = rank of the suffix starting at index i among all suffixes.
    vector<int> cost;  
    // lcp: lcp[i] = length of longest common prefix between suffixes at pos[i] and pos[i+1].
    vector<int> lcp;  
  
    int n = 0;  
    bool donePos = false, doneLcp = false;  
public:  
    suffixArray(string s) {  
        this->s = s;  
        this->s.push_back((char) 'a' - 1);  
        n = (this->s).size();  
        pos.resize(n);  
        cost.resize(n);  
        lcp.resize(n);  
    }  
  
    void countSort() {  
        vector<int> cnt(n);  
        for (auto it: cost) {  
            cnt[it]++;  
        }  
        vector<int> p(n);  
        vector<int> pos_new(n);  
        p[0] = 0;  
        for (int i = 1; i < n; i++) {  
            p[i] = p[i - 1] + cnt[i - 1];  
        }  
        for (auto it: pos) {  
            int i = cost[it];  
            pos_new[p[i]] = it;  
            p[i]++;  
        }  
        pos = pos_new;  
  
    }  
    void computePos() {  
        {  
            vector<pair<int, int>> a(s.size());  
            for (int i = 0; i < s.size(); i++) {  
                a[i] = {s[i], i};  
            }  
            sort(a.begin(), a.end());  
            for (int i = 0; i < n; i++)pos[i] = a[i].second;  
            cost[pos[0]] = 0;  
            for (int i = 1; i < n; i++) {  
                if (a[i].first == a[i - 1].first) {  
                    cost[pos[i]] = cost[pos[i - 1]];  
                } else  
                    cost[pos[i]] = cost[pos[i - 1]] + 1;  
            }  
        }  
        int k = 0;  
        while ((1 << k) <= n) {  
            for (int i = 0; i < n; i++) {  
                pos[i] = (pos[i] - ((1 << k)) + n) % n;  
            }  
            countSort();  
            vector<int> cost_new(n);  
            cost_new[pos[0]] = 0;  
            for (int i = 1; i < n; i++) {  
                pair<int, int> prev = {cost[pos[i - 1]], cost[(pos[i - 1] + (1 << k)) % n]};  
                pair<int, int> cur = {cost[pos[i]], cost[(pos[i] + (1 << k)) % n]};  
                if (prev == cur) {  
                    cost_new[pos[i]] = cost_new[pos[i - 1]];  
                } else  
                    cost_new[pos[i]] = cost_new[pos[i - 1]] + 1;  
            }  
            cost = cost_new;  
            k++;  
        }  
        donePos = true;  
    }  
  
    vector<int> getPos() {  
        if (!donePos)computePos();  
        return pos;  
    }  
    void computeLcp() {  
        // lcp[0]= is between index 0 and 1  
        if (!donePos)computePos();  
        int k = 0;  
        for (int i = 0; i < n - 1; i++) {  
            int pi = cost[i];  
            int j = pos[pi - 1];  
            while (s[i + k] == s[j + k])k++;  
            lcp[pi] = k;  
            k = max(0ll, k - 1);  
        }  
        lcp.erase(lcp.begin());  
        doneLcp = true;  
    }  
  
    vector<int> getLcp() {  
        if (!doneLcp)  
            computeLcp();  
        return lcp;  
    }  
  
    vector<int> getCost() {  
        if (!donePos)  
            computePos();  
        return cost;  
    }  
    string getSub(int st, int len) {  
        string temp = "";  
        for (int j = st; j < st + len; j++)  
            temp += s[j % n];  
        return temp;  
    }  
};
```
### zFunction
```
vector<int> zFunction(vector<int> &s) {  
    int n = s.size();  
    vector<int> z(n);  
    int L = 0, R = 0;  
    for (int i = 1; i < n; i++) {  
        if (i > R) {  
            L = R = i;  
            while (R < n && s[R - L] == s[R]) {  
                R++;  
            }  
            z[i] = R - L;  
            R--;  
        } else {  
            int k = i - L;  
            if (z[k] < R - i + 1) {  
                z[i] = z[k];  
            } else {  
                L = i;  
                while (R < n && s[R - L] == s[R]) {  
                    R++;  
                }  
                z[i] = R - L;  
                R--;  
            }  
        }  
    }  
    return z;  
}
```
### Mo
```
int SQ = 320;  
struct query {  
    ll l, r, idx;  
    bool operator<(query &other) {  
        if (l / SQ != other.l / SQ)return l / SQ < other.l / SQ;  
        return ((l / SQ) & 1) ? r > other.r : r < other.r;  
    }  
};  
signed main() {  
    int n, q;  
    cin >> n >> q;  
    vector<ll> input(n);  
    for (auto &it: input)cin >> it;  
    for (auto &it: input) {  
        if (it > (ll) 1e5)  
            it = 1e5 + 1;  
    }  
    vector<ll> freq(1e5 + 5);  
    vector<query> v(q);  
    int cnt = 0;  
  
    for (int i = 0; i < q; i++) {  
        cin >> v[i].l >> v[i].r;  
        v[i].l--, v[i].r--;  
        v[i].idx = i;  
    }  
    sort(v.begin(), v.end());  
    auto add = [&](ll idx) {  
        cnt -= freq[input[idx]] == input[idx];  
        freq[input[idx]]++;  
        cnt += freq[input[idx]] == input[idx];  
  
    };  
    auto del = [&](ll idx) {  
        cnt -= freq[input[idx]] == input[idx];  
        freq[input[idx]]--;  
        cnt += freq[input[idx]] == input[idx];  
    };  
    auto query = [&]() {  
        return cnt;  
    };  
    ll l = 0, r = -1;  
    vector<ll> ans(q);  
    for (auto &[lq, rq, idx]: v) {  
        while (lq < l)add(--l);  
        while (r < rq)add(++r);  
        while (l < lq)del(l++);  
        while (r > rq)del(r--);  
        ans[idx] = query();  
    }
```
### hash
```
vector<int> bases = {27};  
const int base_sz=1;  
const int  N=1e5+1;  
vector<int> mods = {1000000007};  
int powers[base_sz][N];  
void pre() {  
    for (int i = 0; i < bases.size(); i++)  
        powers[i][0] = 1;  
    for (int i = 0; i < bases.size(); i++)  
        for (int j = 1; j < N; j++) {  
            powers[i][j] = powers[i][j - 1] * bases[i];  
            powers[i][j] %= mods[i];  
        }  
}  
int rhash[base_sz][N]{};  
void make(string &s)  
{  
      
    rhash[0][0]=s.front()-'A'+1;  
  
    for(int i=1;i<s.size();i++)  
    {  
        rhash[0][i]=(rhash[0][i-1]*bases.front()+s[i]-'A'+1)%mods.front();  
    }  
}  
  
int get_rhash(int l,int r,int rhash[1][N])  
{  
    return ((rhash[0][r]-((l==0)?0:rhash[0][l-1]*powers[0][r-l+1]))%mods[0]+mods[0])%mods[0];  
}
```
### Fail
```
vector<ll> failure(string &s) {  
    int n = s.size();  
    vector<ll> fail(n);  
    for (int i = 1; i < n; i++) {  
        int len = fail[i - 1];  
        while (len > 0 && s[i] != s[len])  
            len = fail[len - 1];  
        if (s[i] == s[len])  
            len++;  
        fail[i] = len;  
    }  
    return fail;  
}
```

### NCR WITHOUT MOD
```
long long C(int n, int m){  
    if(m>n-m) m=n-m;  
    long long ans=1;  
    for(int i=0;i<m;i++) ans=ans*(n-i)/(i+1);  
    return ans;  
}
```


### MO on trees
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
int SQ = 320;  
struct query {  
    ll l, r, idx;  
    bool operator<(query &other) {  
        if (l / SQ != other.l / SQ)return l / SQ < other.l / SQ;  
        return ((l / SQ) & 1) ? r > other.r : r < other.r;  
    }  
};  
int N = 40000;  
vector<int> vals(N * 2);  
vector<int> start(N), fin(N);  
vector<ll> input(N);  
vector<int> rev(N * 2);  
vector<vector<int> > adj(N);  
int cnt = 0;  
void dfs(int node,int parent) {  
    vals[cnt] = input[node];  
    rev[cnt] = node;  
    start[node] = cnt++;  
    for (auto it: adj[node]) {  
        if (it == parent)continue;  
        dfs(it, node);  
    }  
    vals[cnt] = input[node];  
    rev[cnt] = node;  
    fin[node] = cnt++;  
}  
signed main() {  
    int n, q;  
    cin >> n >> q;  
    vals = vector<int>(N * 2);  
    start = vector<int>(N);  
    fin = vector<int>(N);  
    input = vector<ll>(N);  
    rev = vector<int>(N * 2);  
    adj = vector<vector<int> >(N);  
    cnt = 0;  
    map<ll,ll> mp;  
    for (int i = 0; i < n; i++) {  
        cin >> input[i];  
        if (mp.count(input[i]))input[i] = mp[input[i]];  
        else {  
            mp[input[i]] = mp.size();  
            input[i] = mp[input[i]];  
        }  
    }  
    for (int i = 0; i < n - 1; i++) {  
        int x, y;  
        cin >> x >> y;  
        x--, y--;  
        adj[x].push_back(y);  
        adj[y].push_back(x);  
    }  
    dfs(0, -1);  
    BL bl(n);  
    bl.edges = adj;  
    bl.dfs(0, -1);  
  
    vector<int> seen(N);  
    vector<ll> freq(1e5 + 5);  
    vector<query> v(q);  
    int cnt = 0;  
    vector<ll> ans(q);  
  
    for (int i = 0; i < q; i++) {  
        cin >> v[i].l >> v[i].r;  
        v[i].l--,  
        v[i].r--;  
        if (bl.get_LCA(v[i].l, v[i].r) != v[i].r && bl.get_LCA(v[i].l, v[i].r) != v[i].l) {  
            if (fin[v[i].l] > start[v[i].r])  
                swap(v[i].l, v[i].r);  
            v[i].l = fin[v[i].l];  
            v[i].r = start[v[i].r];  
        } else {  
            if (bl.get_LCA(v[i].l, v[i].r) == v[i].r) {  
                swap(v[i].l, v[i].r);  
            }  
            v[i].l = start[v[i].l];  
            v[i].r = start[v[i].r];  
        }  
        v[i].idx = i;  
    }  
    sort(v.begin(), v.end());  
    auto add = [&](ll idx) {  
        // seen mean was in bath before like del  
        // not seen add  
        if (seen[rev[idx]]) {  
            freq[input[rev[idx]]]--;  
            if (freq[input[rev[idx]]] == 0)cnt--;  
        } else {  
            freq[input[rev[idx]]]++;  
            if (freq[input[rev[idx]]] == 1)cnt++;  
        }  
        seen[rev[idx]] ^= 1;  
    };  
    auto query = [&]() {  
        return cnt;  
    };  
    ll l = 0, r = -1;  
    for (auto &[lq, rq, idx]: v) {  
        int U = rev[lq], V = rev[rq];  
        if (U == V) {  
            ans[idx] = 1;  
            continue;  
        }  
        while (lq < l)add(--l);  
        while (r < rq)add(++r);  
        while (l < lq)add(l++);  
        while (r > rq)add(r--);  
  
        ans[idx] = query();  
        // corner case node LCA of two nodes on bath  
        if (bl.get_LCA(rev[lq], rev[rq]) != rev[lq] && bl.get_LCA(rev[lq], rev[rq]) != rev[rq]) {  
            if (freq[input[bl.get_LCA(rev[lq], rev[rq])]]==0)  
            ans[idx]++;  
        }  
    }  
    for (auto it: ans)  
        cout << it << endl;;  
}
```


