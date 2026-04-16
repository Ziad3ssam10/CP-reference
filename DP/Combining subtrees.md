```cpp

#include <bits/stdc++.h>
#include <ext/pb_ds/assoc_container.hpp>
#include <ext/pb_ds/tree_policy.hpp>

using namespace std;
using namespace __gnu_pbds;
#define all(v) v.begin(),v.end()
template<typename T>
using orderedset = tree<T, null_type, less<T>, rb_tree_tag, tree_order_statistics_node_update>;
#define ll long long
#define int ll
#define ld double
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

#pragma GCC optimize("O3", "unroll-loops")
#pragma GCC target("avx2", "popcnt")

ll const inf = 1e18;

int const N = 1005;
ll a[N];
vector<int> adj[N];
vector<ll> dp[N];
int n;

vector<int> merge(const vector<int> &dp_root, const vector<int> &dp_child) {
    int n = dp_root.size(), m = dp_child.size();
    vector<int> ret(n + m - 1, -1e18);
    ret[0] = 0;
    // i = 1, MUST take the root
    for (int i = 1; i < n; ++i)
        for (int j = 0; j < m; ++j)
            ret[i + j] = max<ll>(ret[i + j], 0LL + dp_root[i] + dp_child[j]);
    return ret;
}

void dfs(int u, int p) {
    dp[u] = {0, a[u]};
    for (auto &v: adj[u]) {
        if (v == p) continue;
        dfs(v, u);
        dp[u] = merge(dp[u], dp[v]);
    }
}

void solve(int tc) {
    cin >> n;
    for (int i = 1; i <= n; ++i) cin >> a[i];
    for (int i = 1; i < n; ++i) {
        int u, v;
        cin >> u >> v;
        adj[u].push_back(v);
        adj[v].push_back(u);
    }

    dfs(1, -1);

    vector<int> val(n + 1, -1e18);
    for (int i = 1; i <= n; i++) {
        int sz = dp[i].size();
        for (int j = 1; j < sz; j++) {
            val[j] = max(val[j], dp[i][j]);
        }
    }
    ll mx[n + 1][n + 1]{};
    for (int i = 1; i <= n; ++i) {
        ll cur = -1e18;
        for (int j = i; j <= n; ++j) {
            cur = max(cur, val[j]);
            mx[i][j] = cur;
        }
    }

    /*for (int i = 1; i <= n; ++i) cout << dp[1][i] << ' ';
    cout << '\n';
    */

    int q;
    cin >> q;
    while (q--) {
        int l, r;
        cin >> l >> r;
        cout << mx[l][r] << '\n';
    }
}

signed main() {
    wady
    files();
    int t = 1;
    int tc = 1;
    // cin >> t;
    while (t--)
        solve(tc++);
}

```