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

### persistent
```cpp
struct persistent_dsu {
  struct state {
    int u, ru, v, rv;
    state() {
      u = 0;
      ru = 0;
      v = 0;
      rv = 0;
    }
    state(int _u, int _ru, int _v, int _rv) {
      u = _u;
      ru = _ru;
      v = _v;
      rv = _rv;
    }
  };

  int cnt;
  int depth[N], par[N];
  stack<state> st;

  persistent_dsu() {
    cnt = 0;
    memset(depth, 0, sizeof(depth));
    memset(par, 0, sizeof(par));
    while(!st.empty()) st.pop();
  }

  void init(int _sz) {
    cnt = _sz;
    for(int i = 0; i <= _sz; i++)
      par[i] = i, depth[i] = 1;
  }

  int root(int x) {
    if(x == par[x]) return x;
    return root(par[x]);
  }

  bool connected(int x, int y) {
    return root(x) == root(y);
  }

  void unite(int x, int y) {
    int rx = root(x), ry = root(y);
    if(rx == ry) return;

    if(depth[rx] < depth[ry])
      par[rx] = ry;
    else if(depth[ry] < depth[rx])
      par[ry] = rx;
    else par[rx] = ry, depth[ry]++;

    cnt--;
    st.push(state(rx, depth[rx], ry, depth[ry]));

  }

  void snapshot() {
    st.push(state(-1, -1, -1, -1));
  }

  void rollback() {
    while(!st.empty()) {
      if(st.top().u == -1)
        return;

      ++cnt;
      par[st.top().u] = st.top().u;
      par[st.top().v] = st.top().v;
      depth[st.top().u] = st.top().ru;
      depth[st.top().v] = st.top().rv;
      st.pop();
    }
  }
};
```
