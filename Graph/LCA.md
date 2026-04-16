```cpp
const int N = 1e5 + 10, LG = 22;
int n;
int parent[N][LG];
int lvl[N];
vector<int> adj[N];

    void build(int node, int par, int d) {
	    parent[node][0] = par;
	    lvl[node] = d;
	    for (int i = 1; i < LG; i++) {
	        parent[node][i] = parent[parent[node][i - 1]][i - 1];
	    }
	    for (auto &child: adj[node]) {
	        if (child == par)continue;
	        build(child, node, d + 1);
	    }
	}
	
	int kth(int node, int k) {
	    for (int i = LG - 1; i >= 0; i--) {
	        if (k >> i & 1) {
	            node = parent[node][i];
	        }
	    }
	    return node;
	}
	
	int LCA(int u, int v) {
	    if (lvl[u] < lvl[v])swap(u, v);
	    u = kth(u, lvl[u] - lvl[v]);
	    if (u == v)return u;
	    for (int i = LG - 1; i >= 0; i--) {
	        if (parent[u][i] != parent[v][i])u = parent[u][i], v = parent[v][i];
	    }
	    return parent[u][0];
	}
	
	int dist(int u, int v) {
	    int lc = LCA(u, v);
	    return lvl[u] + lvl[v] - 2 * lvl[lc];
	}
```