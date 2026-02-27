```cpp
int const N = 5e5 + 5;
vector<int> adj[N], rev[N], scc[N], comp;
int id[N], n, m;

void dfs(int u, vector<int> g[], bool vis[]) {
    vis[u] = 1;
    for (auto &v: g[u]) {
        if (vis[v]) continue;
        dfs(v, g, vis);
    }
    comp.push_back(u);
}

void build() {
    bool vis[n + 1]{};
    for (int i = 1; i <= n; ++i) {
        if (!vis[i])
            dfs(i, adj, vis);
    }
    auto order = comp;
    reverse(order.begin(),order.end());
    memset(vis, 0, sizeof vis);
    for (auto &i: order) {
        if (!vis[i]) {
            comp.clear();
            dfs(i, rev, vis);
            for (auto &i: comp)
                id[i] = comp.front();
        }
    }
    for (int u = 1; u <= n; ++u) {
        for (auto &v: adj[u]) {
            if (id[u] != id[v]) {
                scc[id[u]].push_back(id[v]);
            }
        }
    }
}

```

```cpp
struct Tarjan {
    int n, compID, timer;
    stack<int> stk;
    set<int> artPoints;
    vector<bool> inStack;
    vector<int> scc, low, tin, id;
    set<pair<int, int>> bridges;
    vector<vector<int>> adj, comps;

    Tarjan(int _n, int _m) {
        n = _n;
        adj = comps = vector<vector<int>>(_n + 1);
        inStack = vector<bool>(_n + 1);
        scc = low = tin = id = vector<int>(_n + 1);
        compID = timer = 0;
    }

    pair<int, int> edge(int u, int v) {
        return {min(u, v), max(u, v)};
    }

    void dfs(int node, int par = -1) {
        // int children = 0; // uncomment for art points
        inStack[node] = true, tin[node] = low[node] = ++timer;
        stk.push(node);
        for (auto &child: adj[node]) {
            // if (child == par)continue; // uncomment for undirected graphs
            if (!tin[child]) {
// children++;
                dfs(child, node);
                low[node] = min(low[node], low[child]);
                // if (low[child] > tin[node]) bridges.insert(edge(node, child));
                // if (low[child] >= tin[node] and (~par or children > 1))
                artPoints.insert(node);
            } else if (inStack[child])
                low[node] = min(low[node], tin[child]);
        }
        // remove for artic and bridges
        if (low[node] == tin[node]) {
            compID++;
            while (true) {
                int cur = stk.top();
                stk.pop();
                inStack[cur] = false;
                scc[cur] = compID;
                comps[compID].push_back(cur);
                if (cur == node)
                    break;
            }
        }
    }

    void bridges_tree(int u) {
        id[u] = timer;
        for (auto &v: adj[u]) {
            auto e = edge(u, v);
            if (bridges.find(e) != bridges.end() or id[v])
                continue;
            bridges_tree(v);
        }
    }

    vector<vector<int>> get_bridges_tree() {
        vector<vector<int>> g(n + 1);
        dfs(1, 1);
        timer = 1;
        for (int i = 1; i <= n; ++i) {
            if (id[i])
                continue;
            bridges_tree(i);
            timer++;
        }
        for (int i = 1; i <= n; ++i)
        {
            for (auto &j: adj[i]) {
                int u = id[i];
                int v = id[j];
                if (u != v)
                    g[u].push_back(v);
            }
        }
        return g;
    }

    vector<vector<int>> getCondensedGraph() {
        vector<vector<int>> g(n + 1);
        for (int i = 1; i <= n; ++i) {
            if (tin[i])
                continue;
            dfs(i);
        }
        for (int i = 1; i <= n; ++i) {
            for (auto &j: adj[i]) {
                if (scc[j] != scc[i])
                    g[scc[i]].push_back(scc[j]);
            }
        }
        return g;
    }
};
```