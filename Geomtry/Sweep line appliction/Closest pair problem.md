```cpp
  
struct Point {  
    int x, y, id;  
};  
  
int dist(Point a, Point b) {  
    int dx = a.x - b.x;  
    int dy = a.y - b.y;  
    return dx * dx + dy * dy;  
}  
  
void solve(int tc) {  
    int n;  
    cin >> n;  
    vector<Point> a(n);  
    for (int i = 0; i < n; i++) {  
        cin >> a[i].x >> a[i].y;  
        a[i].id = i;  
    }  
    sort(a.begin(), a.end(), [](const Point& l, const Point& r) {  
        if (l.x != r.x) return l.x < r.x;  
        return l.y < r.y;  
    });  
    set<pair<int, int>> st;  
    int ans = dist(a[0], a[1]);  
    pair<int, int> indices = {a[0].id, a[1].id};  
    int left = 0;  
    for (int i = 0; i < n; i++) {  
        int d = ceil(sqrt(ans));  
        while (left < i and (a[i].x - a[left].x) * (a[i].x - a[left].x) >= ans) {  
            st.erase({a[left].y, left});  
            left++;  
        }  
        auto it1 = st.lower_bound({a[i].y - d, -1});  
        auto it2 = st.upper_bound({a[i].y + d, n});  
        for (auto it = it1; it != it2; ++it) {  
            int j = it->second;  
            int cur = dist(a[i], a[j]);  
            if (cur < ans) {  
                ans = cur;  
                indices = {a[i].id, a[j].id};  
            }  
        }  
        st.insert({a[i].y, i});  
    }  
    cout << min(indices.first, indices.second) << " "  
         << max(indices.first, indices.second) << " "  
         << fixed << setprecision(6) << sqrt(ans) << endl;  
}
```

```cpp
struct Point {  
    ld x, y;  
};  
  
ld dist(Point a, Point b) {  
    ld dx = a.x - b.x;  
    ld dy = a.y - b.y;  
    return dx * dx + dy * dy;  
}  
  
void solve() {  
    while (true) {  
        int n;  
        cin >> n;  
  
        if (n == 0) return;  
  
        vector<Point> a(n);  
  
        for (int i = 0; i < n; i++)  
            cin >> a[i].x >> a[i].y;  
  
        sort(a.begin(), a.end(), [](auto &l, auto &r) {  
            if (l.x != r.x) return l.x < r.x;  
            return l.y < r.y;  
        });  
  
        set<pair<ld, int> > st;  
  
        ld ans = dist(a[0], a[1]);  
        pair<int, int> best = {0, 1};  
  
        int left = 0;  
  
        for (int i = 0; i < n; i++) {  
            ld d = sqrt(ans);  
  
            while (left < i && (a[i].x - a[left].x) > d) {  
                st.erase({a[left].y, left});  
                left++;  
            }  
  
            auto it1 = st.lower_bound({a[i].y - d, -1});  
            auto it2 = st.upper_bound({a[i].y + d, n});  
  
            for (auto it = it1; it != it2; ++it) {  
                int j = it->second;  
  
                ld cur = dist(a[i], a[j]);  
  
                if (cur < ans) {  
                    ans = cur;  
                    best = {i, j};  
                }  
            }  
  
            st.insert({a[i].y, i});  
        }  
  
        auto print = [&](Point p) {  
            cout << fixed << setprecision(2) << p.x << " " << p.y << " ";  
        };  
  
        if (best.first > best.second)  
            swap(best.first, best.second);  
  
        print(a[best.first]);  
        print(a[best.second]);  
        cout << endl;  
    }  
}
```