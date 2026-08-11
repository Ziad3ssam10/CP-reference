Tested 
```cpp
#include <bits/stdc++.h>  
#include <ext/pb_ds/assoc_container.hpp>  
#include <ext/pb_ds/tree_policy.hpp>  
  
using namespace std;  
using namespace __gnu_pbds;  
template<typename T>  
using orderedset = tree<T, null_type, less<T>, rb_tree_tag, tree_order_statistics_node_update>;  
#define int long long  
#define ll long long  
#define ld long double  
#define endl '\n'  
#define fast_io                      \  
    ios_base::sync_with_stdio(0); \  
    cin.tie(0);                   \  
    cout.tie(0);  
  
void files() {  
#ifndef ONLINE_JUDGE  
    freopen("in.txt", "r", stdin);  
    freopen("out.txt", "w", stdout);  
#endif  
}  
  
const ll mod = 1e9 + 7, N = 2e5 + 5;  
  
using Agg = int; // The in-progress DP state of a node (e.g., intermediate sum, max)  
using Val = int; // The finalized DP state passed to the parent  
  
vector<int> adj[N]; // 0-based nodes  
  
Agg base(int node) {  
    // Initialize the DP state for a node  
    return 0;  
}  
  
Agg merge_into(Agg cur_dp, Val ch_val, int node, int edge_idx) {  
    // Merge a child's finalized value into the current node's aggregate  
    return max(cur_dp, ch_val + 1);  
}  
  
Val finalize_merge(Agg cur_dp, int node, int par_edge_idx) {  
    // Finalize the aggregate before passing it up (add node/edge weights)  
    return cur_dp;  
}  
int n;  
// root_dp[u]: dp of the tree when rooted at u  
// edge_dp[u][i]: dp of the subtree of node adj[u][i] excluding the branch of u  
// redge_dp[u][i]: dp of the tree rooted at u excluding the branch of adj[u][i]  
auto solve_reroot() {  
    vector<Val> root_dp(n), dp(n);  
    vector<vector<Val> > edge_dp(n), redge_dp(n);  
    vector<int> bfs, par(n, -1);  
  
    bfs.reserve(n);  
    bfs.push_back(0);  
    for (int i = 0; i < n; i++) {  
        int u = bfs[i];  
        for (int v: adj[u]) {  
            if (v == par[u]) continue;  
            par[v] = u, bfs.push_back(v);  
        }  
    }  
  
    for (int i = n - 1; i >= 0; i--) {  
        int u = bfs[i], p_idx = -1;  
        Agg agg = base(u);  
        for (int j = 0; j < adj[u].size(); j++) {  
            int v = adj[u][j];  
            if (v == par[u]) p_idx = j;  
            else agg = merge_into(agg, dp[v], u, j);  
        }  
        dp[u] = finalize_merge(agg, u, p_idx);  
    }  
  
    for (int u: bfs) {  
        int deg = adj[u].size();  
        edge_dp[u].resize(deg);  
        redge_dp[u].resize(deg);  
  
        Agg tot = base(u);  
        for (int j = 0; j < deg; j++) {  
            int v = adj[u][j];  
            edge_dp[u][j] = (v == par[u] ? dp[u] : dp[v]);  
            tot = merge_into(tot, edge_dp[u][j], u, j);  
        }  
        root_dp[u] = finalize_merge(tot, u, -1);  
  
        auto rec = [&](auto &self, int l, int r, Agg out) -> void {  
            if (l == r) {  
                redge_dp[u][l] = finalize_merge(out, u, l);  
                if (adj[u][l] != par[u]) dp[adj[u][l]] = redge_dp[u][l];  
                return;  
            }  
            int mid = l + (r - l) / 2;  
            Agg L_out = out, R_out = out;  
            for (int i = mid + 1; i <= r; i++) L_out = merge_into(L_out, edge_dp[u][i], u, i);  
            for (int i = l; i <= mid; i++) R_out = merge_into(R_out, edge_dp[u][i], u, i);  
            self(self, l, mid, L_out), self(self, mid + 1, r, R_out);  
        };  
        if (deg > 0) rec(rec, 0, deg - 1, base(u));  
    }  
    return make_tuple(root_dp, edge_dp, redge_dp);  
}  
  
void solve(int tc) {  
    cin >> n;  
    int m = n - 1;  
    while (m--) {  
        int u, v;  
        cin >> u >> v;  
        u--, v--;  
        adj[u].emplace_back(v);  
        adj[v].emplace_back(u);  
    }  
    auto [rootdp,_,__] = solve_reroot();  
    for (int i = 0; i < n; i++)cout << rootdp[i] << " ";  
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