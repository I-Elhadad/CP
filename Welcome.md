//    #pragma GCC optimize ("O3")  
//    #pragma GCC optimize ("unroll-loops")  
//    #pragma comment(linker, "/STACK:2000000")  
#include<bits/stdc++.h>  
  
using namespace std;  
#define ll int  
#define int int  
#define ld long double  
#define  ui unsigned int  
#define endl "\n"  
#define FOCUS ios_base::sync_with_stdio(0);cin.tie(0);cout.tie(0);  
  
void Go() {  
    ios_base::sync_with_stdio(false), cin.tie(nullptr), cout.tie(nullptr);  
#ifndef ONLINE_JUDGE  
    freopen("input.txt", "r", stdin);  
    freopen("output.txt", "w", stdout);  
#endif  
}  
  
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
    // Go();  
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
    // for (int i = 0; i < n; i++) {  
    //     //cout << i + 1 << " ";    //     for (auto it: adj[i])    //         //cout << it + 1 << " ";    //     //cout << endl;    // }  
  
    dfs(0, -1);  
    // build the LCA structure  
    BL bl(n);  
    bl.edges = adj;  
    bl.dfs(0, -1);  
  
    vector<int> seen(N);  
    for (int i = 0; i < n; i++) {  
        //cout << i + 1 << " " << start[i] << " " << fin[i] << endl;  
    }  
    for (int i = 0; i <= 15; i++) {  
        //cout << vals[i] << " ";  
    }  
    //cout << endl;  
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
  
  
        //cout << v[i].l << " " << v[i].r << endl;  
        // if (v[i].l > v[i].r)        //     swap(v[i].l, v[i].r);        // v[i].l--, v[i].r--;        v[i].idx = i;  
    }  
    sort(v.begin(), v.end());  
    auto add = [&](ll idx) {  
        if (seen[rev[idx]]) {  
            freq[input[rev[idx]]]--;  
            if (freq[input[rev[idx]]] == 0)cnt--;  
        } else {  
            freq[input[rev[idx]]]++;  
            if (freq[input[rev[idx]]] == 1)cnt++;  
        }  
        seen[rev[idx]] ^= 1;  
    };  
    // auto del = [&](ll idx) {  
    //     int val = abs(vals[idx]);    //     freq[val]--;    //     if (freq[val] == 0)cnt--;    // };    // auto customAdd = [&](ll idx) {    //     if (vals[idx] > 0)add(idx);    //     else del(idx);    // };    // auto customDel = [&](ll idx) {    //     if (vals[idx] > 0)del(idx);    //     else add(idx);    // };    auto query = [&]() {  
        return cnt;  
    };  
    ll l = 0, r = -1;  
    // dfs(0,-1);  
    for (auto &[lq, rq, idx]: v) {  
        int U = rev[lq], V = rev[rq];  
        if (U == V) {  
            // path is just that one node  
            ans[idx] = 1;  
            continue;  
        }  
        while (lq < l)add(--l);  
        while (r < rq)add(++r);  
        while (l < lq)add(l++);  
        while (r > rq)add(r--);  
  
        // //cout << l << " " << r << " " << idx << endl;  
        ans[idx] = query();  
        if (bl.get_LCA(rev[lq], rev[rq]) != rev[lq] && bl.get_LCA(rev[lq], rev[rq]) != rev[rq]) {  
            if (freq[input[bl.get_LCA(rev[lq], rev[rq])]]==0)  
            ans[idx]++;  
        }  
    }  
    for (auto it: ans)  
        cout << it << endl;;  
    // //cout<<endl;  
}