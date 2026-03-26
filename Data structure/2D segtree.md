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
  
struct _2dsegtree {  
    int n, m;  
  
    struct Node {  
        int val;  
    };  
  
    Node skip;  
    vector<Node> tree;  
  
    _2dsegtree(int n, int m, vector<vector<int> > &v) {  
        this->n = n;  
        this->m = m;  
        skip.val = 1e15;  
        tree.resize((n * m) << 4, skip);  
        build(1, 0, n - 1, 0, m - 1, v);  
    }  
  
    Node single(int x) {  
        Node r;  
        r.val = x;  
        return r;  
    }  
  
    Node merge(Node a, Node b) {  
        Node ret;  
        ret.val = min(a.val, b.val);  
        return ret;  
    }  
  
    void build(int u, int r1, int r2, int c1, int c2, vector<vector<int> > &a) {  
        if (r1 > r2 || c1 > c2) return;  
        if (r1 == r2 && c1 == c2) return void(tree[u] = single(a[r1][c1]));  
        int mid_r = r1 + r2 >> 1;  
        int mid_c = c1 + c2 >> 1;  
        build((u << 2) - 2, r1, mid_r, c1, mid_c, a);  
        build((u << 2) - 1, mid_r + 1, r2, c1, mid_c, a);  
        build((u << 2), r1, mid_r, mid_c + 1, c2, a);  
        build((u << 2) | 1, mid_r + 1, r2, mid_c + 1, c2, a);  
  
        tree[u] = merge(  
            merge(tree[(u << 2) - 2], tree[(u << 2) - 1]),  
            merge(tree[(u << 2)], tree[(u << 2) | 1])  
        );  
    }  
  
    void update(int u, int r1, int r2, int c1, int c2, int x, int y, int val) {  
        if (r1 > r2 || c1 > c2 || x < r1 || x > r2 || y < c1 || y > c2) return;  
        if (r1 == r2 && c1 == c2) return void(tree[u] = single(val));  
  
        int mid_r = r1 + r2 >> 1;  
        int mid_c = c1 + c2 >> 1;  
  
        update((u << 2) - 2, r1, mid_r, c1, mid_c, x, y, val);  
        update((u << 2) - 1, mid_r + 1, r2, c1, mid_c, x, y, val);  
        update((u << 2), r1, mid_r, mid_c + 1, c2, x, y, val);  
        update((u << 2) | 1, mid_r + 1, r2, mid_c + 1, c2, x, y, val);  
  
        tree[u] = merge(  
            merge(tree[(u << 2) - 2], tree[(u << 2) - 1]),  
            merge(tree[(u << 2)], tree[(u << 2) | 1])  
        );  
    }  
  
    Node query(int u, int r1, int r2, int c1, int c2, int qr1, int qr2, int qc1, int qc2) {  
        if (r1 > r2 || c1 > c2 || r1 > qr2 || r2 < qr1 || c1 > qc2 || c2 < qc1) return skip;  
        if (r1 >= qr1 && r2 <= qr2 && c1 >= qc1 && c2 <= qc2) return tree[u];  
  
        int mid_r = r1 + r2 >> 1;  
        int mid_c = c1 + c2 >> 1;  
  
        return merge(  
            merge(query((u << 2) - 2, r1, mid_r, c1, mid_c, qr1, qr2, qc1, qc2),  
                  query((u << 2) - 1, mid_r + 1, r2, c1, mid_c, qr1, qr2, qc1, qc2)),  
            merge(query((u << 2), r1, mid_r, mid_c + 1, c2, qr1, qr2, qc1, qc2),  
                  query((u << 2) | 1, mid_r + 1, r2, mid_c + 1, c2, qr1, qr2, qc1, qc2))  
        );  
    }  
  
    void update(int x, int y, int val) {  
        --x, --y;  
        update(1, 0, n - 1, 0, m - 1, x, y, val);  
    }  
  
    Node query(int r1, int c1, int r2, int c2) {  
        --r1, --c1, --r2, --c2;  
        return query(1, 0, n - 1, 0, m - 1, r1, r2, c1, c2);  
    }  
};  
  
void solve(int tc) {  
    int n, m, p;  
    cin >> n >> m >> p;  
    vector<vector<int> > dp(n + 2, vector<int>(m + 2, 1e13));  
    _2dsegtree tr1(n + 2, m + 2, dp);  
    _2dsegtree tr2(n + 2, m + 2, dp);  
    _2dsegtree tr3(n + 2, m + 2, dp);  
    _2dsegtree tr4(n + 2, m + 2, dp);  
    vector<pair<int,int> > type[p + 2];  
    for (int i = 1; i <= n; i++) {  
        for (int j = 1; j <= m; j++) {  
            int x;  
            cin >> x;  
            type[x].emplace_back(i, j);  
        }  
    }  
    for (auto [x,y]: type[1]) {  
        dp[x][y] = abs(x - 1) + abs(y - 1);  
    }  
    for (int i = 1; i < p; i++) {  
        if (type[i].empty() or type[i + 1].empty())continue;  
        for (auto [x,y]: type[i]) {  
            tr1.update(x, y, dp[x][y] - x - y);  
            tr2.update(x, y, dp[x][y] - x + y);  
            tr3.update(x, y, dp[x][y] + x - y);  
            tr4.update(x, y, dp[x][y] + x + y);  
        }  
        for (auto [r, c]: type[i + 1]) {  
            int ans1 = tr1.query(1, 1, r, c).val + r + c;  
            int ans2 = tr2.query(1, c, r, m).val + r - c;  
            int ans3 = tr3.query(r, 1, n, c).val - r + c;  
            int ans4 = tr4.query(r, c, n, m).val - r - c;  
  
            dp[r][c] = min({ans1, ans2, ans3, ans4});  
        }  
        for (auto [r, c]: type[i]) {  
            tr1.update(r, c, 1e15);  
            tr2.update(r, c, 1e15);  
            tr3.update(r, c, 1e15);  
            tr4.update(r, c, 1e15);  
        }  
    }  
    if (!type[p].empty()) {  
        auto [r, c] = type[p][0];  
        cout << dp[r][c] << endl;  
    } else {  
        cout << 0 << endl;  
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