```cpp

int const N = 2e5 + 5;

int sz[N], heavy[N];

vector<int> adj[N];

void preDfs(int u, int p) { // CALL

    sz[u] = 1;

    for (auto &v : adj[u]) {

        if (v == p) continue;

        preDfs(v, u);

        sz[u] += sz[v];

        if (sz[v] > sz[heavy[u]])

            heavy[u] = v;

    }

}

void update(int u, int d) {

    // Logic Add , Rem

}

void collect(int u, int p, int d) {

    update(u, d);

    for (auto &v : adj[u]) {

        if (v == p) continue;

        collect(v, u, d);

    }

}

void dfs(int u, int p, bool keep) { // CALL

    for (auto &v : adj[u]) {

        if (v == p || heavy[u] == v) continue;

        dfs(v, u, 0); // Keep The Heavy

    }

    if (heavy[u]) dfs(heavy[u], u, 1);

    update(u, 1);

    for (auto &v : adj[u]) {

        if (v == p || v == heavy[u]) continue; // Didn't Remove The Heavy

        collect(v, u, +1);  

    }

    // ans the query for the subtree of u

    if (!keep) collect(u, p, -1);

}

```