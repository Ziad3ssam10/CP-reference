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
  
#define multihash array<int,2>  
const int mod[] = {  
    (int) 1e9 + 7, (int) 1e9 + 9  
};  
const int N = 1e5 + 20, B = 2;  
const int b[] = {200003, 200009};  
int pw[B][N], inv[B][N], invb[B];  
int add(int a,int b,int id) {  
    return (a + b + mod[id]) % mod[id];  
}  
  
int mul(int a,int b,int id) {  
    return (a * b) % mod[id];  
}  
  
int fp(int b,int p,int id) {  
    if (p == 0)return 1;  
    int ret = fp(b, p / 2, id);  
    ret = mul(ret, ret, id);  
    if (p & 1)ret = mul(ret, b, id);  
    return ret;  
}  
  
int invv(int x,int id) {  
    return fp(x, mod[id] - 2, id);  
}  
  
void pre() {  
    for (int i = 0; i < B; i++) {  
        pw[i][0] = inv[i][0] = 1;  
        invb[i] = invv(b[i], i);  
    }  
    for (int i = 1; i < N; i++) {  
        for (int base = 0; base < B; base++) {  
            pw[base][i] = mul(pw[base][i - 1], b[base], base);  
            inv[base][i] = mul(inv[base][i - 1], invb[base], base);  
        }  
    }  
}  
  
struct Segmenttree {  
    int n;  
  
    struct node {  
        multihash pref = {0, 0}, suff = {0, 0};  
        int len = 0;  
    } skip;  
  
    vector<node> tree;  
  
    Segmenttree(int n) {  
        this->n = n;  
        tree.resize(n << 2);  
    }  
  
    node single(int val) {  
        node ret;  
        ret.pref = ret.suff = {val, val};  
        ret.len = 1;  
        return ret;  
    }  
  
    node merge(node l, node r) {  
        if (l.len == 0) return r;  
        if (r.len == 0) return l;  
  
        node ret;  
        ret.len = l.len + r.len;  
  
        for (int i = 0; i < B; i++) {  
            ret.pref[i] = add(l.pref[i], mul(r.pref[i], pw[i][l.len], i), i);  
            ret.suff[i] = add(r.suff[i], mul(l.suff[i], pw[i][r.len], i), i);  
        }  
        return ret;  
    }  
  
    void update(int u,int st,int en,int idx,int val) {  
        if (idx > en or idx < st)return;  
        if (st == en)return void(tree[u] = single(val));  
        int mid = (st + en) >> 1;  
        update(u << 1, st, mid, idx, val);  
        update(u << 1 | 1, mid + 1, en, idx, val);  
        tree[u] = merge(tree[u << 1], tree[u << 1 | 1]);  
    }  
  
    node query(int u,int st,int en,int l,int r) {  
        if (st >= l and en <= r)return tree[u];  
        if (st > r or en < l)return skip;  
        int mid = (st + en) >> 1;  
        return merge(query(u << 1, st, mid, l, r), query(u << 1 | 1, mid + 1, en, l, r));  
    }  
  
    void update(int idx,int val) {  
        idx--;  
        update(1, 0, n - 1, idx, val);  
    }  
  
    bool query(int l,int r) {  
        l--, r--;  
        auto ret = query(1, 0, n - 1, l, r);  
        return (ret.pref == ret.suff);  
    }  
};  
  
int idx(char ch) {  
    return ch - 'a' + 1;  
}  
  
void solve(int tc) {  
    int n, q;  
    cin >> n >> q;  
    string s;  
    cin >> s;  
    int idd = 1;  
    Segmenttree segtree(n + 5);  
    for (auto &i: s) {  
        segtree.update(idd++, idx(i));  
    }  
    while (q--) {  
        int t;  
        cin >> t;  
        if (t == 1) {  
            int id;  
            cin >> id;  
            char ch;  
            cin >> ch;  
            segtree.update(id, idx(ch));  
        } else {  
            int l, r;  
            cin >> l >> r;  
            if (segtree.query(l, r)) {  
                cout << "Adnan Wins\n";  
            } else cout << "ARCNCD!\n";  
        }  
    }  
}  
  
signed main() {  
    fast_io  
    files();  
    pre();  
    int t = 1;  
    cin >> t;  
    int tc = 1;  
    while (t--)  
        solve(tc++);  
}
```