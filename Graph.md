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