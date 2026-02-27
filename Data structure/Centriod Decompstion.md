```cpp
const int N = 1e5 + 20;
int n, sz[N], par2[N];
bool done[N];
vector<int> adj[N], adj2[N]; //adj is original tree, adj2 is centroid tree

int update_sz(int node, int par) {
    sz[node] = 1;
    for (auto ch: adj[node]) {
        if (ch == par || done[ch]) continue;
        update_sz(ch, node);
        sz[node] += sz[ch];
    }
    return sz[node];
}

int get_centroid(int node, int par, int n) {
    int mx = 0;
    for (auto ch: adj[node]) {
        if (ch == par || done[ch]) continue;
        if (sz[ch] > sz[mx])
            mx = ch;
    }

    if (sz[mx] * 2 > n)
        return get_centroid(mx, node, n);
    else
        return node;
}

int decompose(int node, int par, int d) {
    int cen = get_centroid(node, par, update_sz(node, par));
    done[cen] = 1;
    if (par != node) {
        adj2[par].push_back(cen);
        par2[cen] = par;
    }

    for (auto ch: adj[cen]) {
        if (done[ch]) continue;
        decompose(ch, cen, d + 1);
    }

    return cen;
}
```