```cpp

// N * sqrt(sum of lens) ---- vaild to 5e5 it will be  ~ 3e8
// if it all uniuqe it will be  ( n ^ 2)
struct Aho {
    int n;
    const int Alpha = 26;
    vector<vector<int> > nxt, out;
    vector<int> link, outlink;
    // nxt[][] at each node it tells u where to fail if i add that char
    // link[] at each node the lPS
    // outlink[] at each node the lps i can match and has a pattern
    // out [][] at each node the indcies for the patterns i match it
    int node() {
        link.emplace_back(0);
        outlink.emplace_back(0);
        nxt.emplace_back(Alpha, 0);
        out.emplace_back(0);
        return n++;
    }

    Aho() : n(0) {
        node();
    };

    int idx(char c) {
        return c - 'a';
    }

    void add_pat(string &pat,int i) {
        int u = 0;
        for (auto c: pat) {
            if (nxt[u][idx(c)] == 0) nxt[u][idx(c)] = node();
            u = nxt[u][idx(c)];
        }
        out[u].emplace_back(i);
    }

    void build() {
        queue<int> q;
        for (q.push(0); !q.empty();) {
            int u = q.front();
            q.pop();
            for (int c = 0; c < Alpha; ++c) {
                int v = nxt[u][c];
                if (!v) nxt[u][c] = nxt[link[u]][c];
                else {
                    link[v] = u ? nxt[link[u]][c] : 0;
                    outlink[v] = out[link[v]].empty() ? outlink[link[v]] : link[v];
                    q.push(v);
                }
            }
        }
    }

    int go(int u, char c) {
        while (u and !nxt[u][idx(c)]) u = link[u];
        u = nxt[u][idx(c)];
        return u;
    }
};
```

other 
```cpp
struct Aho {
    struct Node {
        array<int, 26> nxt;
        int link;
        vector<int> out;
 
        Node() {
            nxt.fill(-1);
            link = -1;
        }
    };
 
    vector<Node> t;
    Aho() { t.emplace_back(); }
    int add_string(const string &s, int id) {
        int v = 0;
        for (char c: s) {
            int x = c - 'a';
            if (t[v].nxt[x] == -1) {
                t[v].nxt[x] = t.size();
                t.emplace_back();
            }
            v = t[v].nxt[x];
        }
        t[v].out.push_back(id);
        return v;
    }
 
    void build() {
        queue<int> q;
        for (int c = 0; c < 26; c++) {
            int u = t[0].nxt[c];
            if (u != -1) {
                t[u].link = 0;
                q.push(u);
            } else t[0].nxt[c] = 0;
        }
        while (!q.empty()) {
            int v = q.front();
            q.pop();
            for (int c = 0; c < 26; c++) {
                int u = t[v].nxt[c];
                if (u != -1) {
                    t[u].link = t[t[v].link].nxt[c];
                    for (int id: t[t[u].link].out) t[u].out.push_back(id);
                    q.push(u);
                } else {
                    t[v].nxt[c] = t[t[v].link].nxt[c];
                }
            }
        }
    }
};
```