```cpp
// Tested
void solve(int tc) {  
    int n, m, k;  
    cin >> n >> m >> k;  
    vector<set<int> > adj(n + 5);  
    while (m--) {  
        int u, v;  
        cin >> u >> v;  
        adj[u].emplace(v);  
        adj[v].emplace(u);  
    }  
    set<int> st;  
    for (int i = 2; i <= n; i++) {  
        st.emplace(i);  
    }  
    vector<int> coms(n + 2);  
    queue<int> q;  
    vector<int> todo;  
    int comps = 0;  
    while (st.size()) {  
        auto node = *st.begin();  
        q.emplace(node);  
        comps++;  
        st.erase(node);  
        while (q.size()) {  
            auto curr = q.front();  
            q.pop();  
            coms[curr] = comps;  
            todo.clear();  
  
            for (auto child: st) {  
                if (adj[curr].count(child) == 0) {  
                    todo.emplace_back(child);  
                }  
            }  
  
            for (auto &i: todo) {  
                st.erase(i);  
                q.emplace(i);  
            }  
        }  
    }  
    if (adj[1].size() > (n - k - 1))cout << "impossible\n";  
    else if (comps > k)cout << "impossible\n";  
    else {  
        bool can = 1;  
        vector<bool> temp(comps + 2);  
        for (int i = 1; i <= n; i++) {  
            if (!adj[i].count(1))temp[coms[i]] = 1;  
        }  
        for (int i = 1; i <= comps; i++) {  
            can &= temp[i];  
        }  
        if (can)cout << "possible\n";  
        else cout << "impossible\n";  
    }  
}  
  
  
```