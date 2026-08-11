
## Definition

An Eulerian path is a path in a graph that visits every edge exactly once. An Eulerian circuit is an Eulerian path that starts and ends at the same vertex.

## Existence Conditions

### For Undirected Graphs

- A graph has an Eulerian path if and only if:
- All edges belong to the same connected component
- Either all vertices have even degree, or exactly two vertices have odd degree (the start and end vertices)
- A graph has an Eulerian circuit if and only if all vertices have even degree
- Undirected :

### For Directed Graphs

- A graph has an Eulerian path if and only if:
- All edges belong to the same connected component
- Either all vertices have equal in-degree and out-degree, or one vertex has out-degree = in-degree + 1 (start vertex), another has in-degree = out-degree + 1 (end vertex), and all others have equal in-degree and out-degree
- A graph has an Eulerian circuit if and only if all vertices have equal in-degree and out-degree

```cpp
// 1-based 
// Tested
struct Eulerian {
    int n;
    bool directed;
    int edge_cnt = 0;
    vector<vector<pair<int,int>>> g; // (to, edgeID)
    vector<int> in, out;             // degree counters
    vector<int> used;                // mark edges used in DFS
    vector<int> ptr;                 // iterator per node
    vector<int> path;                // final result
 
    Eulerian(int _n, bool _directed = false) {
        n = _n;
        directed = _directed;
        g.assign(n + 1, {});
        in.assign(n + 1, 0);
        out.assign(n + 1, 0);
    }
 
    void addEdge(int u, int v) {
        ++edge_cnt;
        g[u].push_back({v, edge_cnt});
        out[u]++, in[v]++;
        if (!directed) {
            g[v].push_back({u, edge_cnt});
            out[v]++, in[u]++;
        }
    }
 
    void dfs(int u) {
        while (ptr[u] < (int)g[u].size()) {
            auto [v, id] = g[u][ptr[u]++];
            if (used[id]) continue;
            used[id] = 1;
            dfs(v);
        }
        path.push_back(u);
    }
 
    bool solve() {
        path.clear();
        ptr.assign(n + 1, 0);
        used.assign(edge_cnt + 1, 0);
 
        int start = 0, end = 0, root = 0;
 
        if (!directed) {
            // undirected: at most 2 odd-degree vertices
            for (int i = 1; i <= n; i++) {
                if (out[i] & 1) {
                    if (!start) start = i;
                    else if (!end) end = i;
                    else return false;
                }
            }
            if (!start) { // all even
                for (int i = 1; i <= n; i++)
                    if (out[i]) { start = i; break; }
            }
            if (!start) return true; // empty graph
            root = start;
        }
        else {
            // directed: in/out degree constraints
            int cnt_start = 0, cnt_end = 0;
            for (int i = 1; i <= n; i++) {
                if (out[i] - in[i] == 1) { cnt_start++; start = i; }
                else if (in[i] - out[i] == 1) cnt_end++;
                else if (abs(in[i] - out[i]) > 1) return false;
            }
            if ((cnt_start != 1 || cnt_end != 1) && cnt_start != 0) return false;
            if (!start) {
                for (int i = 1; i <= n; i++)
                    if (out[i]) { start = i; break; }
            }
            if (!start) return true; // empty graph
            root = start;
        }
 
        dfs(root);
 
        // connectivity check
        if ((int)path.size() != edge_cnt + 1) return false;
 
        reverse(path.begin(), path.end());
        return true;
    }
 
    bool isCircuit() {
        if (!solve()) return false;  // if no Eulerian path, can't be circuit
        if (!directed) {
            for (int i = 1; i <= n; i++)
                if (out[i] & 1) return false;
            return true;
        }
        for (int i = 1; i <= n; i++)
            if (in[i] != out[i]) return false;
        return true;
    }
 
    vector<int> getPath() { return path; }
};
```