
### SCC
```
struct SCC {  
    int n;  
    vector<vector<int>> edges;  
    stack<int> stk;  
    int id = 1;  
    vector<int> instk, lowlink, dfn, comp;  
    vector<vector<int>> comps;  
    vector<pair<int, int>> bridges;  
  
    SCC(int n = 0) : n(n) {  
        edges.resize(n + 1);  
        instk.resize(n + 1, 0);  
        lowlink.resize(n + 1, 0);  
        dfn.resize(n + 1, 0);  
        comp.resize(n + 1, -1);  
    }  
  
    void dfs(int node, int cnt, int parent = -1) {  
        lowlink[node] = dfn[node] = id++;  
        instk[node] = 1;  
        stk.push(node);  
        cnt++;  
        for (int it: edges[node]) {  
            if (it == parent)continue;  
            if (dfn[it] == 0) {  
                dfs(it, cnt, node);  
                lowlink[node] = min(lowlink[it], lowlink[node]);  
            } else if (instk[it]) {  
                lowlink[node] = min(dfn[it], lowlink[node]);  
            }  
  
        }  
        if (lowlink[node] == dfn[node] && ~parent) {  
            bridges.push_back({min(node, parent), max(node, parent)});  
        }  
  
        if (lowlink[node] == dfn[node]) {  
            int x = -1;  
            while (x != node) {  
                x = stk.top();  
                stk.pop();  
                instk[x] = 0;  
            }  
        }  
    }  
  
    void start() {  
        for (int i = 1; i <= n; i++) {  
            if (dfn[i] == 0) {  
                dfs(i, 0);  
            }  
        }  
    }  
};
```
###  2-Sat
```
struct SAT {  
    int n;  
    int id = 1;  
    bool is_solvable = true;  
    vector<int> cmp_root_node, comp_assigned_values, assigned_values;  
    int cnt;  
    SCC scc;  
  
    SAT(int n) : n(n) {  
        scc = SCC(n);  
        cnt = 0;  
    }  
  
    void addOr(int a, int b) {  
        // a or b = ~a->b and ~b->a  
        scc.edges[NOT(a)].push_back(b);  
        scc.edges[NOT(b)].push_back(a);  
    }  
  
    void addXor(int a, int b) {  
        // a xor b = (a or b) and (~a or ~b);  
        addOr(a, b);  
        addOr(NOT(a), NOT(b));  
    }  
  
    void addMustT(int a) {  
        scc.edges[NOT(a)].push_back(a);  
    }  
  
    void addMustF(int a) {  
        scc.edges[a].push_back(NOT(a));  
    }  
  
    void addImplies(int a, int b) {  
        // a->b  =  -b -> -a  
        scc.edges[a].push_back(b);  
        scc.edges[NOT(b)].push_back(NOT(a));  
    }  
  
    void addBiImplies(int a, int b) {  
        addImplies(a, b);  
        addImplies(b, a);  
    }  
    // 0 first value  
    // 1 not first value    int NOT(int i) {  
        return i ^ 1;  
    }  
  
    int POS(int i) {  
        return i *= 2;  
    }  
  
    bool start() {  
        scc.start();  
        for (int i = 0; i < n; i += 2) {  
            if (scc.comp[i] == scc.comp[NOT(i)]) {  
                is_solvable = false;  
                break;  
            }  
        }  
        if(!is_solvable)  
        return is_solvable;  
        comp_assigned_values = vector<int>((int) scc.comps.size(), -1);  
        for (int i = 0; i < scc.comps.size(); i++) {  
            if (comp_assigned_values[i] == -1) {  
                comp_assigned_values[i] = 1;  
                comp_assigned_values[scc.comp[(NOT(cmp_root_node[i]))]] = 0;  
            }  
        }  
        for (int i = 0; i < n; i++) {  
            assigned_values[i] = comp_assigned_values[scc.comp[i]];  
        }  
        return is_solvable;  
    }  
};
```

![[Screenshot from 2025-04-24 00-54-22.png]]



### solving_for_diffrent_constrains 
```
vector<int>solving_for_diffrent_constrains(int n,vector<array<int,3>>constrains)
{
// general rules
/*
a=b
a   ----> b
*/
    vector<array<int,3>>edges;// first add ralations
    for (auto [x1,x2,c]: constrains) {
        //   x2-x1==c    ----><
        // x2-x1>=c    ------> x1->x2 with c
        // -x2+x1>=-c   ---->   x1-x2>=-c    x2->x1  with -c

        edges.push_back({x1, x2, c});
        edges.push_back({x2, x1, -c});
    }

    for (int i = 1; i <= n; i++) {
        edges.push_back({i - 1, i, 1});
    }
    // base value is zero
    second put base value and loop on number of nodes
    vector<int> dis(n + 1, 0);
    bool cyc=false;
    for (int i = 0; i <= n; i++) {
        for (auto [x,y,c]: edges) {
            if (dis[y] < dis[x] + c) {
                dis[y] = dis[x] + c;
                if (i==n)cyc=true;
            }
        }
    }

}
```

| Relation | Difference Form              | Graph Edge                                           |
| -------- | ---------------------------- | ---------------------------------------------------- |
| `x ≤ y`  | `x - y ≤ 0`                  | Edge: `y → x` with weight `0`                        |
| `x < y`  | `x - y ≤ -1`                 | Edge: `y → x` with weight `-1`                       |
| `x ≥ y`  | `y - x ≤ 0`                  | Edge: `x → y` with weight `0`                        |
| `x > y`  | `y - x ≤ -1`                 | Edge: `x → y` with weight `-1`                       |
| `x = y`  | both `x ≤ y` and `y ≤ x`     | Edges: `y → x` and `x → y`, weight `0` each          |
| `x ≠ y`* | `x - y ≤ -1` or `y - x ≤ -1` | One of the edges `y → x` or `x → y` with weight `-1` |

_\*Note:_ For `≠`, you must choose either `x < y` or `y < x` to violate equality._

### belman Get all nodes affected by negative cycle and detect cycle

```
int n, m, q, s;  
cin >> n >> m >> q >> s;  
vector<array<ll, 3> > edges(m);  
for (int i = 0; i < m; i++) {  
    cin >> edges[i][0] >> edges[i][1] >> edges[i][2];  
}  
vector<ll> dis(n, 1e18);  
dis[s] = 0;  
ll INF = 1e18;  
bool isCycle=0;  
for (int i = 0; i < n; i++) {  
    for (auto [x,y,c]: edges) {  
        if (dis[x] != INF && dis[y] > dis[x] + c) {  
            dis[y] = dis[x] + c;  
            if (i==n-1)isCycle=true;  
        }  
    }  
}  
for (int i = 0; i < n; i++) {  
    for (auto [x,y,c]: edges) {  
        if (dis[x] != INF && dis[y] > dis[x] + c) {  
            dis[y] = -INF;  
        }  
    }  
}  
while (q--) {  
    int end;  
    cin >> end;  
    if (dis[end] == -1e18) {  
        cout << "-Infinity" << endl;  
    } else if (dis[end] == 1e18) {  
        cout << "Impossible" << endl;  
    } else  
        cout << dis[end] << endl;  
}
```

### Detect negative cycle using belman
```
cin >> n >> m;
    vector<array<ll, 3> > edges(m);
    for (int i = 0; i < m; i++) {
        cin >> edges[i][0] >> edges[i][1] >> edges[i][2], edges[i][0]--, edges[i][1]--;
    }
    vector<int> dis(n, 0);
    ll INF = 1e18;
    int cyc = -1;
    vector<int> parent(n,-1);
    for (int i = 0; i < n; i++) {
        for (auto [x,y,c]: edges) {
            if ( dis[y] > dis[x] + c) {
                dis[y] = dis[x] + c;
                parent[y] = x;
                if (i == n - 1)cyc = y;
            }
        }
    }
    if (cyc==-1) {
        cout << "NO" << endl;
    } else {
        cout<<"YES"<<endl;
        for (int i=0;i<n;i++)cyc=parent[cyc];
        vector<int>v={cyc};
        int st=cyc;
        while (parent[st]!=cyc) {
            st=parent[st];
            v.push_back(st);
        }
        v.push_back(cyc);
        reverse(v.begin(),v.end());
        for (auto it:v)
            cout<<it+1<<" ";
        cout<<endl;


    }
```
