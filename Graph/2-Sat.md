```cpp
int n, m, r[N];

vector<int> adj[2 * N], rev[2 * N], sw[2 * N];

vector<bool> used;
vector<int> order, comp;
vector<bool> assignment;

void dfs1(int v) {
    used[v] = true;

    for (int u : adj[v]) {
        if (!used[u])
            dfs1(u);
    }

    order.push_back(v);
}

void dfs2(int v, int cl) {
    comp[v] = cl;

    for (int u : rev[v]) {
        if (comp[u] == -1)
            dfs2(u, cl);
    }
}

bool solve_2SAT(int n) {
    order.clear();

    used.assign(n, false);

    for (int i = 0; i < n; ++i) {
        if (!used[i])
            dfs1(i);
    }

    comp.assign(n, -1);

    for (int i = 0, j = 0; i < n; ++i) {
        int v = order[n - i - 1];

        if (comp[v] == -1)
            dfs2(v, j++);
    }

    assignment.assign(n / 2, false);

    for (int i = 0; i < n; i += 2) {
        if (comp[i] == comp[i + 1])
            return false;

        assignment[i / 2] = comp[i] > comp[i + 1];
    }

    return true;
}

int V(int a) {
    return (a << 1);
}

int Ne(int a) {
    return (a << 1) | 1;
}

void edge(int a, int b) {
    adj[a].push_back(b);
    rev[b].push_back(a);
}

void OR(int a, int b) {
    edge(a ^ 1, b);
    edge(b ^ 1, a);
}

void XNOR(int a, int b) {
    OR(Ne(a), V(b));
    OR(V(a), Ne(b));
}

void XOR(int a, int b) {
    OR(Ne(a), Ne(b));
    OR(V(a), V(b));
}

int main() {
    cin >> n >> m;

    for (int i = 1; i <= n; i++)
        cin >> r[i];

    for (int i = 1; i <= m; i++) {
        int k;
        cin >> k;

        for (int j = 1; j <= k; j++) {
            int x;
            cin >> x;

            sw[x].push_back(i);
        }
    }

    for (int i = 1; i <= n; i++) {
        int p = sw[i][0] - 1;
        int q = sw[i][1] - 1; // zero based

        if (!r[i]) {
            XOR(p, q);
        } else {
            XNOR(p, q);
        }
    }

    bool ans = solve_2SAT(2 * m);

    if (ans)
        cout << "YES\n";
    else
        cout << "NO\n";

    return 0;
}
```