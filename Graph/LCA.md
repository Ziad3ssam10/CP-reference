```cpp
TEsted
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
	
	bool is_ancestor(int u,int v) {  
    return in[u] <= in[v] and out[u] >= out[v];;  
}
```

## o(1)
```cpp 
const int N = 1e5 + 20, LG = 22;  
vector<int> adj[N];  
int up[N][LG];  
int depth[N];  
int n;  
int st[2 * N][LG], lg[2 * N];  
int timer = 0;  
vector<int> flat;  
vector<int> depflat;  
int in[N];  
  
void build(int node,int par,int d) {  
    depth[node] = d;  
    up[node][0] = par;  
    in[node] = flat.size();  
    flat.emplace_back(node);  
    depflat.emplace_back(d);  
    for (auto child: adj[node]) {  
        if (child == par)continue;  
        build(child, node, d + 1);  
        flat.emplace_back(node);  
        depflat.emplace_back(d);  
    }  
}  
  
void buildst() {  
    int m = flat.size();  
    lg[1] = 0;  
    for (int i = 2; i <= m; i++)lg[i] = lg[i / 2] + 1;  
    for (int i = 0; i < m; i++)st[i][0] = i;  
    for (int j = 1; (1 << j) <= m; j++) {  
        for (int i = 0; i + (1 << j) <= m; i++) {  
            int x = st[i][j - 1];  
            int y = st[i + (1 << (j - 1))][j - 1];  
            st[i][j] = (depflat[x] < depflat[y]) ? x : y;  
        }  
    }  
}  
  
int lcao1(int u,int v) {  
    int l = in[u];  
    int r = in[v];  
    if (l > r)swap(l, r);  
    int j = lg[r - l + 1];  
    int x = st[l][j];  
    int y = st[r - (1 << j) + 1][j];  
    return (depflat[x] < depflat[y]) ? flat[x] : flat[y];  
}

```