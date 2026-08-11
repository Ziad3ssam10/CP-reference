```cpp

struct BlockCutTree {  
    int n, timer, bcc_cnt;  
  
    // adj stores the original graph  
    // tree stores the resulting Block-Cut Tree   
    vector<vector<int> > adj, tree;  
    vector<int> dfn, low, stk;  
  
    BlockCutTree(int _n) {  
        n = _n;  
        timer = bcc_cnt = 0;  
  
        adj.resize(n + 1);  
        tree.resize(2 * n + 1);  
  
        dfn.assign(n + 1, 0);  
        low.assign(n + 1, 0);  
    }  
  
    void add_edge(int u, int v) {  
        adj[u].push_back(v);  
        adj[v].push_back(u);  
    }  
  
    void dfs(int u, int p = 0) {  
        dfn[u] = low[u] = ++timer;  
        stk.push_back(u);  
  
        for (int v: adj[u]) {  
            if (v == p) continue;  
  
            if (dfn[v]) {  
                low[u] = min(low[u], dfn[v]);  
            } else {  
                dfs(v, u);  
                low[u] = min(low[u], low[v]);  
  
                if (low[v] >= dfn[u]) {  
                    bcc_cnt++;  
                    int bcc_node = n + bcc_cnt;  
                    while (true) {  
                        int curr = stk.back();  
                        stk.pop_back();  
                        tree[bcc_node].push_back(curr);  
                        tree[curr].push_back(bcc_node);  
                        if (curr == v) break;  
                    }  
                    tree[bcc_node].push_back(u);  
                    tree[u].push_back(bcc_node);  
                }  
            }  
        }  
    }  
  
    void build() {  
        for (int i = 1; i <= n; i++) {  
            if (!dfn[i]) {  
                dfs(i);  
                stk.clear();  
            }  
        }  
    }  
};  
  
void solve(int tc) {  
    int n, m;  
    cin >> n >> m;  
    BlockCutTree bct(n + 5);  
    while (m--) {  
        int u, v;  
        cin >> u >> v;  
        bct.add_edge(u, v);  
    }  
    bct.build();  
    vector<int> ans;  
    for (int i = 1; i <= n; i++) {  
        if (bct.tree[i].size() > 1)ans.emplace_back(i);  
    }  
    cout << ans.size() << endl;  
    for (auto &i: ans)cout << i << " ";  
    cout << endl;  
}  
  
  
signed main() {  
    fast_io  
    files();  
    int t = 1;  
    int tc = 1;  
    while (t--)  
        solve(tc++);  
}
```