
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
  
const int B = 20;  
  
  
void solve(int tc) {  
    int n, m;  
    cin >> n >> m;  
    vector<DSU> dsus(B, DSU(n + 2));  
    while (m--) {  
        int i1, j1, i2, j2;  
        cin >> i1 >> j1 >> i2 >> j2;  
        int len = j1 - i1 + 1;  
        for (int i = B - 1; i >= 0; i--) {  
            if (len & (1ll << i)) {  
                dsus[i].unionset(i1, i2);  
                i1 += (1ll << i);  
                i2 += (1ll << i);  
            }  
        }  
    }  
    for (int j = B - 1; j >= 1; j--) {  
        for (int i = 1; i <= n; i++) {  
            int par = dsus[j].find(i);  
            if (par == i)continue;  
            dsus[j - 1].unionset(i, par);  
            int offset = (1ll << (j - 1));  
            dsus[j - 1].unionset(i + offset, par + offset);  
        }  
    }  
    set<int> st;  
    for (int i = 1; i <= n; i++) {  
        st.insert(dsus[0].find(i));  
    }  
    cout << st.size() << endl;  
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