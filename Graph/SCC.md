```cpp
struct Tarjan {  
    int n, compID, timer;  
    bool is_directed;  
    stack<int> stk;  
    set<int> artPoints;  
    vector<bool> inStack;  
    vector<int> scc, low, tin, id;  
    set<pair<int, int> > bridges;  
    vector<vector<int> > adj, comps;  
  
    // Constructor: 1-based indexing by default.  
    Tarjan(int _n, bool _is_directed = true) {  
        n = _n;  
        is_directed = _is_directed;  
        adj.assign(n + 1, vector<int>());  
        comps.assign(n + 1, vector<int>());  
        inStack.assign(n + 1, false);  
        scc.assign(n + 1, 0);  
        low.assign(n + 1, 0);  
        tin.assign(n + 1, 0);  
        id.assign(n + 1, 0);  
        compID = timer = 0;  
    }  
  
    void add_edge(int u, int v) {  
        adj[u].push_back(v);  
        if (!is_directed) adj[v].push_back(u);  
    }  
  
    pair<int, int> get_edge(int u, int v) {  
        return {min(u, v), max(u, v)};  
    }  
  
    void dfs(int node, int par = -1) {  
        int children = 0;  
        inStack[node] = true;  
        tin[node] = low[node] = ++timer;  
        stk.push(node);  
  
        for (auto &child: adj[node]) {  
            if (!is_directed && child == par) continue;  
  
            if (!tin[child]) {  
                children++;  
                dfs(child, node);  
                low[node] = min(low[node], low[child]);  
                if (!is_directed) {  
                    if (low[child] > tin[node]) {  
                        bridges.insert(get_edge(node, child));  
                    }  
                    if (low[child] >= tin[node] && par != -1) {  
                        artPoints.insert(node);  
                    }  
                }  
            } else if (inStack[child] || !is_directed) {  
                low[node] = min(low[node], tin[child]);  
            }  
        }  
  
        if (!is_directed && par == -1 && children > 1) {  
            artPoints.insert(node);  
        }  
  
        if (low[node] == tin[node]) {  
            compID++;  
            while (true) {  
                int cur = stk.top();  
                stk.pop();  
                inStack[cur] = false;  
                scc[cur] = compID;  
                comps[compID].push_back(cur);  
                if (cur == node) break;  
            }  
        }  
    }  
  
    void build() {  
        for (int i = 1; i <= n; ++i) {  
            if (!tin[i]) dfs(i);  
        }  
    }  
  
    void _build_bridge_components(int u) {  
        id[u] = timer;  
        for (auto &v: adj[u]) {  
            if (bridges.count(get_edge(u, v)) || id[v]) continue;  
            _build_bridge_components(v);  
        }  
    }  
  
    vector<vector<int> > get_bridge_tree() {  
        assert(!is_directed);  
        build();  
        timer = 0;  
        fill(id.begin(), id.end(), 0);  
  
        for (int i = 1; i <= n; ++i) {  
            if (!id[i]) {  
                ++timer;  
                _build_bridge_components(i);  
            }  
        }  
  
        vector<vector<int> > tree(timer + 1);  
        for (auto &e: bridges) {  
            int u = id[e.first];  
            int v = id[e.second];  
            tree[u].push_back(v);  
            tree[v].push_back(u);  
        }  
        return tree;  
    }  
  
    vector<vector<int> > get_condensed_graph() {  
        assert(is_directed);  
        build();  
        vector<vector<int> > dag(compID + 1);  
        set<pair<int, int> > seen_edges;  
  
        for (int i = 1; i <= n; ++i) {  
            for (auto &j: adj[i]) {  
                if (scc[i] != scc[j]) {  
                    if (seen_edges.insert({scc[i], scc[j]}).second) {  
                        dag[scc[i]].push_back(scc[j]);  
                    }  
                }  
            }  
        }  
        return dag;  
    }  
};  
  
void solve(int tc) {  
    int n, m;  
    cin >> n >> m;  
    Tarjan tr(n, 1);  
    vector<int> a(n + 1);  
    for (int i = 1; i <= n; i++) {  
        cin >> a[i];  
    }  
    while (m--) {  
        int u, v;  
        cin >> u >> v;  
        tr.add_edge(u, v);  
    }  
    tr.build();  
    auto adj = tr.get_condensed_graph();  
    vector<int> dp(tr.compID + 2);  
    for (int i = 1; i <= n; i++) {  
        dp[tr.scc[i]] += a[i];  
    }  
    vector<int> in(tr.compID + 2);  
    for (int i = 1; i <= tr.compID; i++) {  
        for (auto &v: adj[i])in[v]++;  
    }  
    queue<int> q;  
    vector<int> topo;  
    for (int i = 1; i <= tr.compID; i++) {  
        if (!in[i])q.emplace(i);  
    }  
    while (q.size()) {  
        auto node = q.front();  
        q.pop();  
        topo.emplace_back(node);  
        for (auto child: adj[node]) {  
            if (!(--in[child])) {  
                q.emplace(child);  
            }  
        }  
    }  
    int ans = 0;  
    reverse(topo.begin(), topo.end());  
    for (auto &i: topo) {  
        int maxi = 0;  
        for (auto child: adj[i]) {  
            maxi = max(maxi, dp[child]);  
        }  
        dp[i] += maxi;  
        ans = max(ans, dp[i]);  
    }  
    cout << ans << endl;  
}
```