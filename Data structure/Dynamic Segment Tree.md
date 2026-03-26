```cpp
#include <bits/stdc++.h>
using namespace std;

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
  
struct implicitsegtree {  
    struct Node {  
        int freq = 0;  
        int lazy = 0;  
        int left = -1;  
        int right = -1;  
    };  
  
    vector<Node> tree;  
    const int n;  
    int timer = 0;  
  
    int merge(int a, int b) { return a + b; }  
  
    void apply(int cur, int len, int val) {  
        if (val == 1) {  
            tree[cur].lazy = val;  
            tree[cur].freq = len * val;  
        }  
    }  
  
    void push_down(int cur, int l, int r) {  
        if (tree[cur].left == -1) {  
            tree[cur].left = ++timer;  
            tree.push_back(Node());  
        }  
        if (tree[cur].right == -1) {  
            tree[cur].right = ++timer;  
            tree.push_back(Node());  
        }  
        int m = (l + r) / 2;  
        apply(tree[cur].left, m - l + 1, tree[cur].lazy);  
        apply(tree[cur].right, r - m, tree[cur].lazy);  
        tree[cur].lazy = 0;  
    }  
  
    void update(int cur, int l, int r, int ql, int qr, int val) {  
        if (qr < l || ql > r) { return; }  
        if (ql <= l && r <= qr) {  
            apply(cur, r - l + 1, val);  
        } else {  
            push_down(cur, l, r);  
            int m = (l + r) / 2;  
            update(tree[cur].left, l, m, ql, qr, val);  
            update(tree[cur].right, m + 1, r, ql, qr, val);  
            tree[cur].freq =  
                    merge(tree[tree[cur].left].freq, tree[tree[cur].right].freq);  
        }  
    }  
  
    int query(int cur, int l, int r, int ql, int qr) {  
        if (qr < l || ql > r) { return 0; }  
        if (ql <= l && r <= qr) { return tree[cur].freq; }  
        push_down(cur, l, r);  
        int m = (l + r) / 2;  
        return merge(query(tree[cur].left, l, m, ql, qr),  
                    query(tree[cur].right, m + 1, r, ql, qr));  
    }  
  
    implicitsegtree(int n, int q = 0) : n(n) {  
        if (q > 0) { tree.reserve(2 * q * __lg(n)); }  
        tree.push_back(Node());  
    }  
  
    void update(int ql, int qr, int val) { update(0, 0, n - 1, ql, qr, val); }  
  
    int query(int ql, int qr) { return query(0, 0, n - 1, ql, qr); }  
};  
  
void solve(int tc) {  
    int q;  
    cin >> q;  
    implicitsegtree tr((int) 1e9 + 2, q);  
    int prv = 0;  
    while (q--) {  
        int t, l, r;  
        cin >> t >> l >> r;  
        if (t == 1) {  
            int ans = tr.query(l + prv, r + prv);  
            cout << ans << endl;  
            prv = ans;  
        } else {  
            tr.update(l + prv, r + prv, 1);  
        }  
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