#### Subtree ranges 
$$[in[u], out[u]]$$
#### Path ranges 
* Case 1 : u is ancestor of v $$ [in[u],in[v]]$$
* Case 2 : $$[out[u], in[v]]$$ and add LCA manually 
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
#define wady                      \  
    ios_base::sync_with_stdio(0); \  
    cin.tie(0);                   \  
    cout.tie(0);  
  
void files() {  
#ifndef ONLINE_JUDGE  
    freopen("in.txt", "r", stdin);  
    freopen("out.txt", "w", stdout);  
#endif  
}  
  
const int N = 200005, M = 1e6 + 20, LG = 20;  
int SQ;  
vector<int> adj[N];  
int in[N], out[N], lvl[N];  
int flat[2 * N], a[N], ans[M];  
int up[N][LG];  
int timer = 0;  
int n, q;  
bool seen[N];  
int frq[N];  
int cnt = 0;  
  
void dfs(int node, int par) {  
    in[node] = ++timer;  
    up[node][0] = par;  
    flat[timer] = node;  
    lvl[node] = lvl[par] + 1;  
    for (int i = 1; i < LG; i++) {  
        up[node][i] = up[up[node][i - 1]][i - 1];  
    }  
    for (auto child: adj[node]) {  
        if (child == par) continue;  
        dfs(child, node);  
    }  
    out[node] = ++timer;  
    flat[timer] = node;  
}  
  
struct query {  
    int l, r, lc, idx;  
  
    query(int _l, int _r, int _lc, int _idx) {  
        l = _l;  
        r = _r;  
        lc = _lc;  
        idx = _idx;  
    }  
  
    bool operator<(const query &oth) const {  
        int b1 = l / SQ, b2 = oth.l / SQ;  
        if (b1 != b2) return b1 < b2;  
        return (b1 & 1) ? r < oth.r : r > oth.r;  
    }  
};  
  
int LCA(int x, int y) {  
    if (lvl[x] > lvl[y]) { swap(x, y); }  
    int diff = lvl[y] - lvl[x];  
    for (int i = 0; (1 << i) <= diff; i++) {  
        if ((1 << i) & diff) { y = up[y][i]; }  
    }  
    if (x == y) { return x; }  
    for (int i = LG - 1; i >= 0; i--) {  
        if (up[x][i] != up[y][i]) {  
            x = up[x][i];  
            y = up[y][i];  
        }  
    }  
    return up[x][0];  
}  
  
void check(int node) {  
    if (seen[node]) {  
        frq[a[node]]--;  
        if (frq[a[node]] == 0) cnt--;  
    } else {  
        if (frq[a[node]] == 0) cnt++;  
        frq[a[node]]++;  
    }  
    seen[node] ^= 1;  
}  
  
void MO(vector<query> &queries) {  
    int l = 1, r = 0;  
    for (auto [ql, qr, lc, idx]: queries) {  
        while (l > ql) check(flat[--l]);  
        while (r < qr) check(flat[++r]);  
        while (l < ql) check(flat[l++]);  
        while (r > qr) check(flat[r--]);  
        if (lc != -1) check(lc);  
        ans[idx] = cnt;  
        if (lc != -1) check(lc);  
    }  
}  
  
void solve(int tc) {  
    cin >> n >> q;  
  
    timer = 0;  
    cnt = 0;  
    for (int i = 0; i <= n; i++) {  
        adj[i].clear();  
        seen[i] = false;  
        frq[i] = 0;  
    }  
  
    vector<int> vals;  
    for (int i = 1; i <= n; i++) {  
        cin >> a[i];  
        vals.push_back(a[i]);  
    }  
  
    sort(vals.begin(), vals.end());  
    vals.erase(unique(vals.begin(), vals.end()), vals.end());  
    for (int i = 1; i <= n; i++) {  
        a[i] = lower_bound(vals.begin(), vals.end(), a[i]) - vals.begin() + 1;  
    }  
  
    int m = n - 1;  
    SQ = max(1LL, (long long)sqrt(2 * n + 2));  
    while (m--) {  
        int u, v;  
        cin >> u >> v;  
        adj[u].emplace_back(v);  
        adj[v].emplace_back(u);  
    }  
  
    dfs(1, 1);  
  
    vector<query> queries;  
    for (int i = 1; i <= q; i++) {  
        int u, v;  
        cin >> u >> v;  
        if (in[u] > in[v]) swap(u, v);  
        int lc = LCA(u, v);  
        if (lc == u) {  
            queries.emplace_back(in[u], in[v], -1, i);  
        } else {  
            queries.emplace_back(out[u], in[v], lc, i);  
        }  
    }  
  
    sort(queries.begin(), queries.end());  
    MO(queries);  
  
    for (int i = 1; i <= q; i++) {  
        cout << ans[i] << endl;  
    }  
}  
  
signed main() {  
    wady  
    files();  
    int t = 1;  
    int tc = 1;  
    while (t--)  
        solve(tc++);  
}
```
