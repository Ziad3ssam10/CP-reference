```cpp
struct DSU {
    vector<int> sz, parent;

    DSU(int n) {
        sz.resize(n + 1, 1);
        parent.resize(n + 1);;
        for (int i = 0; i <= n; i++) {
            parent[i] = i;
        }
    }

    int find(int node) {
        if (node == parent[node])
            return node;
        return parent[node] = find(parent[node]);
    }

    void unionset(int u, int v) {
        u = find(u);
        v = find(v);
        if (u == v) {
            return;
        }
        if (sz[u] < sz[v]) {
            parent[u] = v;
            sz[v] += sz[u];
        } else {
            parent[v] = u;
            sz[u] += sz[v];
        }
    }
};
```

### Roll back DSU
```cpp
struct RollBackUnionFind {
    using elem_tp = tuple<int, int, int, int>;

    vector<int> node, size_vec;
    stack<elem_tp> info;

    RollBackUnionFind(int N) : node(N), size_vec(N, 1) {
        for (int i = 0; i < N; i++) node[i] = i;
    }

    int find(int u) {
        return node[u] == u ? u : find(node[u]);
    }

    int size(int u) {
        return size_vec[find(u)];
    }

    bool unite(int u, int v) {
        u = find(u), v = find(v);
        info.emplace(u, node[u], v, node[v]);
        if (u == v) return false;

        if (size(u) > size(v)) swap(u, v);
        node[u] = v;
        size_vec[v] += size_vec[u];
        return true;
    }

    int same(int u, int v) {
        return find(u) == find(v);
    }

    void rollback() {
        assert(info.size());
        int u, nu, v, nv;
        tie(u, nu, v, nv) = info.top();
        info.pop();
        node[u] = nu, node[v] = nv;
    }
};
```