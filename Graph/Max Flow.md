```cpp
struct FlowEdge {
    int v, u;
    long long cap, flow = 0;
 
    FlowEdge(int v, int u, long long cap) : v(v), u(u), cap(cap) {}
};
struct Dinic {
    const long long flow_inf = 1e18;
    vector<FlowEdge> edges;
    vector<vector<int>> adj;
    int n, m = 0;
    int s, t;
 
    vector<int> level, ptr;
    queue<int> q;
 
    Dinic(int n, int s, int t) : n(n), s(s), t(t) {
        adj.resize(n);
        level.resize(n);
        ptr.resize(n);
    }
 
    void add_edge(int v, int u,int cap = 1) {
        edges.emplace_back(v, u, cap);
        adj[v].push_back(m++);
        edges.emplace_back(u, v, 0);
        adj[u].push_back(m++);
    }
 
    bool bfs() {
        while (!q.empty()) {
            int v = q.front();
            q.pop();
            for (int id: adj[v]) {
                if (edges[id].cap == edges[id].flow)
                    continue;
                if (level[edges[id].u] != -1)
                    continue;
                level[edges[id].u] = level[v] + 1;
                q.push(edges[id].u);
            }
        }
        return level[t] != -1;
    }
 
    long long dfs(int v, long long pushed) {
        if (pushed == 0)
            return 0;
        if (v == t)
            return pushed;
        for (int &cid = ptr[v]; cid < (int) adj[v].size(); cid++) {
            int id = adj[v][cid];
            int u = edges[id].u;
            if (level[v] + 1 != level[u])
                continue;
            long long tr = dfs(u, min(pushed, edges[id].cap - edges[id].flow));
            if (tr == 0)
                continue;
            edges[id].flow += tr;
            edges[id ^ 1].flow -= tr;
            return tr;
        }
        return 0;
    }
 
    void dfs(int nd, vector<vector<int>> &ret, vector<int> &cur) {
        cur.push_back(nd);
        if (nd == t) {
            ret.push_back(cur);
            cur.pop_back();
            return;
        }
 
        for (int &cid = ptr[nd]; cid < (int) adj[nd].size();) {
            int id = adj[nd][cid];
            cid++;
            if ((id & 1) || (edges[id].flow == 0))continue;
            int u = edges[id].u;
            dfs(u, ret, cur);
            if (nd != s) {
                break;
            }
        }
 
        if (cur.empty()) {
            cout << -1;
            return;
        }
        cur.pop_back();
    }
 
    long long flow() {
        long long f = 0;
        while (true) {
            fill(level.begin(), level.end(), -1);
            level[s] = 0;
            q.push(s);
            if (!bfs())
                break;
            fill(ptr.begin(), ptr.end(), 0);
            while (long long pushed = dfs(s, flow_inf)) {
                f += pushed;
            }
        }
        return f;
    }
 
    vector<vector<int>> paths() {
        vector<vector<int>> ret;
        vector<int> cur;
        fill(ptr.begin(), ptr.end(), 0);
        dfs(s, ret, cur);
        return ret;
    }
};
```