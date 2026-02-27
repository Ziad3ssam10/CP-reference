
1. Maximum Bipartite Matching
    
    - A matching is a set of edges such that no two share a vertex.
    - Kuhn’s algorithm (DFS based augmenting paths) finds maximum matching.
    - Complexity: O(V * E) where V = n (left part), E = edges.
2. Minimum Vertex Cover
    
    - A vertex cover = set of vertices touching all edges.
    - Minimum Vertex Cover = smallest such set.
    - König’s Theorem (for bipartite graphs): size(MVC) = size(Maximum Matching).
    - We can construct the set using BFS/DFS after Kuhn.
```cpp


struct Kuhn {
    int n, m;                    // left size (n), right size (m)
    vector<vector<int>> g;       // adjacency list from left → right
    vector<int> mt;              // match for right side
    vector<int> used;            // visited marker for left
    int timer = 1;

    Kuhn(int n, int m) : n(n), m(m) {
        g.assign(n, {});
        mt.assign(m, -1);
        used.assign(n, 0);
    }

    void addEdge(int u, int v) {
        // u ∈ [0..n-1], v ∈ [0..m-1]
        g[u].push_back(v);
    }

    bool dfs(int v) {
        if (used[v] == timer) return false;
        used[v] = timer;
        for (int to : g[v]) {
            if (mt[to] == -1 || dfs(mt[to])) {
                mt[to] = v;
                return true;
            }
        }
        return false;
    }

    int maxMatching() {
        int matchSize = 0;
        for (int v = 0; v < n; v++) {
            timer++;
            if (dfs(v)) matchSize++;
        }
        return matchSize;
    }

    vector<pair<int,int>> getMatching() {
        vector<pair<int,int>> res;
        for (int r = 0; r < m; r++) {
            if (mt[r] != -1) res.push_back({mt[r], r});
        }
        return res;
    }

    // ─────────────────────────────────────────────────────────────
    // Construct Minimum Vertex Cover using König’s theorem
    // Returns: {leftCover, rightCover}
    // Complexity: O(V + E)
    // ─────────────────────────────────────────────────────────────
    pair<vector<int>, vector<int>> minVertexCover() {
        maxMatching();  // ensure we have maximum matching

        vector<int> visL(n, 0), visR(m, 0);
        queue<int> q;

        // Start BFS from unmatched vertices in left part
        vector<int> matchL(n, -1);
        for (int r = 0; r < m; r++)
            if (mt[r] != -1) matchL[mt[r]] = r;

        for (int u = 0; u < n; u++) {
            if (matchL[u] == -1) { 
                q.push(u);
                visL[u] = 1;
            }
        }

        // BFS alternating between free/matched edges
        while (!q.empty()) {
            int u = q.front(); q.pop();
            for (int v : g[u]) {
                if (!visR[v] && matchL[u] != v) {
                    visR[v] = 1;
                    if (mt[v] != -1 && !visL[mt[v]]) {
                        visL[mt[v]] = 1;
                        q.push(mt[v]);
                    }
                }
            }
        }

        // Left cover = all not visited
        // Right cover = all visited
        vector<int> coverL, coverR;
        for (int u = 0; u < n; u++)
            if (!visL[u]) coverL.push_back(u);
        for (int v = 0; v < m; v++)
            if (visR[v]) coverR.push_back(v);

        return {coverL, coverR};
    }
};

```


### Hopcroft
```cpp
#include<bits/stdc++.h>
using namespace std;

const int N = 3e5 + 9;

struct HopcroftKarp {
  static const int inf = 1e9;
  int n;
  vector<int> l, r, d;
  vector<vector<int>> g;
  HopcroftKarp(int _n, int _m) {
    n = _n;
    int p = _n + _m + 1;
    g.resize(p);
    l.resize(p, 0);
    r.resize(p, 0);
    d.resize(p, 0);
  }
  void add_edge(int u, int v) {
    g[u].push_back(v + n); //right id is increased by n, so is l[u]
  }
  bool bfs() {
    queue<int> q;
    for (int u = 1; u <= n; u++) {
      if (!l[u]) d[u] = 0, q.push(u);
      else d[u] = inf;
    }
    d[0] = inf;
    while (!q.empty()) {
      int u = q.front();
      q.pop();
      for (auto v : g[u]) {
        if (d[r[v]] == inf) {
          d[r[v]] = d[u] + 1;
          q.push(r[v]);
        }
      }
    }
    return d[0] != inf;
  }
  bool dfs(int u) {
    if (!u) return true;
    for (auto v : g[u]) {
      if(d[r[v]] == d[u] + 1 && dfs(r[v])) {
        l[u] = v;
        r[v] = u;
        return true;
      }
    }
    d[u] = inf;
    return false;
  }
  int maximum_matching() {
    int ans = 0;
    while (bfs()) {
      for(int u = 1; u <= n; u++) if (!l[u] && dfs(u)) ans++;
    }
    return ans;
  }
};
int32_t main() {
  ios_base::sync_with_stdio(0);
  cin.tie(0);
  int n, m, q;
  cin >> n >> m >> q;
  HopcroftKarp M(n, m);
  while (q--) {
    int u, v;
    cin >> u >> v;
    M.add_edge(u, v);
  }
  cout << M.maximum_matching() << '\n';
  return 0;
}
```
