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
  
  
const int N = 2e5 + 20;  
  
struct persistent_dsu {  
    struct state {  
        int u, ru, v, rv;  
  
        state(int _u, int _ru, int _v, int _rv) : u(_u), ru(_ru), v(_v), rv(_rv) {  
        }  
    };  
  
    int cnt;  
    int depth[N], par[N];  
    stack<state> st;  
  
    void init(int _sz) {  
        cnt = _sz;  
        for (int i = 0; i <= _sz; i++) {  
            par[i] = i;  
            depth[i] = 1;  
        }  
        while (!st.empty()) st.pop();  
    }  
  
    int root(int x) {  
        while (x != par[x]) x = par[x];  
        return x;  
    }  
  
    void unite(int x, int y) {  
        int rx = root(x), ry = root(y);  
        if (rx == ry) return;  
        if (depth[rx] < depth[ry]) swap(rx, ry);  
        st.push(state(ry, depth[ry], rx, depth[rx]));  
        par[ry] = rx;  
        if (depth[rx] == depth[ry]) depth[rx]++;  
        cnt--;  
    }  
  
    void snapshot() {  
        st.push(state(-1, -1, -1, -1));  
    }  
  
    void rollback() {  
        while (!st.empty() && st.top().u != -1) {  
            state s = st.top();  
            st.pop();  
            par[s.u] = s.u;  
            depth[s.u] = s.ru;  
            par[s.v] = s.v;  
            depth[s.v] = s.rv;  
            cnt++;  
        }  
        if (!st.empty()) st.pop();  
    }  
} dsu;  
  
struct query {  
    int l, r, idx;  
  
    query(int _l, int _r, int _idx) : l(_l), r(_r), idx(_idx) {  
    }  
};  
  
int sq;  
  
bool cmp(const query &f, const query &s) {  
    int bf = f.l / sq;  
    int bs = s.l / sq;  
    if (bf != bs) return bf < bs;  
    return f.r < s.r;  
}  
  
void solve(int tc) {  
    int n, m, q;  
    cin >> n >> m;  
  
    sq = max(1LL, (int) sqrt(m));  
    vector<pair<int, int> > edges(m + 1);  
    for (int i = 1; i <= m; i++) cin >> edges[i].first >> edges[i].second;  
    cin >> q;  
    vector<query> queries;  
    vector<int> ans(q + 1);  
  
    for (int i = 1; i <= q; i++) {  
        int l, r;  
        cin >> l >> r;  
        if (l / sq == r / sq) {  
            dsu.init(n);  
            for (int j = l; j <= r; j++) {  
                auto [u,v] = edges[j];  
                dsu.unite(u, v);  
            }  
            ans[i] = dsu.cnt;  
        } else {  
            queries.emplace_back(l, r, i);  
        }  
    }  
  
    sort(queries.begin(), queries.end(), cmp);  
  
    int lstblk = -1, border = 0, r_ptr = 0;  
  
    for (auto &qu: queries) {  
        int cur_blk = qu.l / sq;  
        if (cur_blk != lstblk) {  
            dsu.init(n);  
            border = (cur_blk + 1) * sq;  
            r_ptr = border;  
            lstblk = cur_blk;  
        }  
  
        while (r_ptr < qu.r) {  
            r_ptr++;  
            dsu.unite(edges[r_ptr].first, edges[r_ptr].second);  
        }  
  
        dsu.snapshot();  
        for (int k = qu.l; k <= border; k++) {  
            dsu.unite(edges[k].first, edges[k].second);  
        }  
        ans[qu.idx] = dsu.cnt;  
        dsu.rollback();  
    }  
  
    for (int i = 1; i <= q; i++) cout << ans[i] << endl;  
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