```cpp

    int n, m, q;
    cin >> n >> m >> q;
    const int INF = 1e15;
    vector<vector<int>> d(n + 1, vector<int>(n + 1, INF));
    // the self edge is zero 
    for (int i = 1; i <= n; i++) d[i][i] = 0;
    while (m--) {
        int u, v, c;
        cin >> u >> v >> c;
        d[u][v] = min(d[u][v], c);
        d[v][u] = min(d[v][u], c);
    }
    // floyed 
    
    for (int k = 1; k <= n; k++)
        for (int i = 1; i <= n; i++)
            for (int j = 1; j <= n; j++)
                if (d[i][k] < INF and d[k][j] < INF)
                    d[i][j] = min(d[i][j], d[i][k] + d[k][j]);
    while (q--) {
        int a, b;
        cin >> a >> b;
        int ans = min(d[a][b], d[b][a]);
        if (ans == INF) cout << "-1\n";
        else cout << ans << endl;
    }
```

```cpp
struct Edge {
    int a, b, cost;
};

int n, m, v;
vector<Edge> edges;
const int INF = 1000000000;

void solve()
{
    vector<int> d(n, INF);
    d[v] = 0;
    for (int i = 0; i < n - 1; ++i)
        for (Edge e : edges)
            if (d[e.a] < INF)
                d[e.b] = min(d[e.b], d[e.a] + e.cost);
    // display d, for example, on the screen
}
```