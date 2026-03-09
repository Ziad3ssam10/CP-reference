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
  
const int N = 2e5 + 10;  
  
struct Event {  
    int x, y1, y2, type;  
    bool operator<(const Event &other) const {  
        if (x != other.x) return x < other.x;  
        return type > other.type;  
    }  
};  
  
int cnt[N * 8];  
int len[N * 8];  
int y_coords[N];  
  
void update_node(int node, int tl, int tr) {  
    if (cnt[node] > 0) {  
        len[node] = y_coords[tr + 1] - y_coords[tl];  
    } else if (tl != tr) {  
        len[node] = len[node << 1] + len[node << 1 | 1];  
    } else {  
        len[node] = 0;  
    }  
}  
  
void update(int node, int tl, int tr, int ql, int qr, int val) {  
    if (ql > tr || qr < tl) return;  
    if (ql <= tl && tr <= qr) {  
        cnt[node] += val;  
        update_node(node, tl, tr);  
        return;  
    }  
    int mid = (tl + tr) >> 1;  
    update(node << 1, tl, mid, ql, qr, val);  
    update(node << 1 | 1, mid + 1, tr, ql, qr, val);  
    update_node(node, tl, tr);  
}  
  
void solve(int tc) {  
    int n;  
    cin >> n;  
    vector<Event> events;  
    vector<int> ys;  
    for (int i = 0; i < n; i++) {  
        int x1, y1, x2, y2;  
        cin >> x1 >> y1 >> x2 >> y2;  
        if (x1 > x2) swap(x1, x2);  
        if (y1 > y2) swap(y1, y2);  
        events.push_back({x1, y1, y2, 1});  
        events.push_back({x2, y1, y2, -1});  
        ys.push_back(y1);  
        ys.push_back(y2);  
    }  
    sort(events.begin(), events.end());  
    sort(ys.begin(), ys.end());  
    ys.erase(unique(ys.begin(), ys.end()), ys.end());  
    for (int i = 0; i < ys.size(); i++) y_coords[i] = ys[i];  
    int m = ys.size();  
    fill(cnt, cnt + 8 * m, 0);  
    fill(len, len + 8 * m, 0);  
    int ans = 0;  
    for (int i = 0; i < events.size(); i++) {  
        if (i > 0) ans += len[1] * (events[i].x - events[i - 1].x);  
        int ql = lower_bound(ys.begin(), ys.end(), events[i].y1) - ys.begin();  
        int qr = lower_bound(ys.begin(), ys.end(), events[i].y2) - ys.begin() - 1;  
        if (ql <= qr) update(1, 0, m - 2, ql, qr, events[i].type);  
    }  
    cout << ans << endl;  
}  
  
  
signed main() {  
    wady  
    int t = 1;  
    int tc = 1;  
    while (t--)  
        solve(tc++);  
}
```