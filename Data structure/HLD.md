```cpp
const int N = 2e5 + 20;
int par[N], depth[N], heavy[N], head[N], pos[N];
int cur_pos = 0;
vector<int> adj[N];
int n;

struct segmenttree {
    int sz;
    vector<int> tree;

    segmenttree() {}

    segmenttree(int _n) {
        sz = _n;
        tree.assign(4 * sz, 0);
    }

    int merge(int node) {
        return max(tree[node * 2 + 1], tree[node * 2 + 2]);
    }

    void update(int idx, int val, int node = 0, int lx = 0, int rx = -1) {
        if (rx == -1) rx = sz - 1;

        if (lx == rx) {
            tree[node] = val;
            return;
        }

        int mid = (lx + rx) >> 1;
        if (idx <= mid)
            update(idx, val, node * 2 + 1, lx, mid);
        else
            update(idx, val, node * 2 + 2, mid + 1, rx);

        tree[node] = merge(node);
    }

    int query(int l, int r, int node = 0, int lx = 0, int rx = -1) {
        if (rx == -1) rx = sz - 1;

        if (lx > r || rx < l)
            return -1e18;

        if (lx >= l && rx <= r)
            return tree[node];

        int mid = (lx + rx) >> 1;
        return max(
            query(l, r, node * 2 + 1, lx, mid),
            query(l, r, node * 2 + 2, mid + 1, rx)
        );
    }
}seg;

int dfs(int node) {
    int sz = 1;
    int max_chsz = 0;
    for (auto &child: adj[node]) {
        if (child == par[node])continue;
        par[child] = node;
        depth[child] = depth[node] + 1;
        int chsz = dfs(child);
        sz += chsz;
        if (chsz > max_chsz) {
            max_chsz = chsz;
            heavy[node] = child;
        }
    }
    return sz;
}

void decompse(int node,int h) {
    head[node] = h;
    pos[node] = cur_pos++;
    if (heavy[node] != -1) {
        decompse(heavy[node], h);
    }
    for (auto child: adj[node]) {
        if (child == par[node] or child == heavy[node])continue;
        decompse(child, child);
    }
}


void init() {
    fill(par, par + n + 5, 0);
    fill(depth, depth + n + 5, 0);
    fill(heavy, heavy + n + 5, -1);
    fill(head, head + n + 5, 0);
    fill(pos, pos + n + 5, 0);
    cur_pos = 0;
    dfs(0);
    decompse(0, 0);
}

int query(int a,int b) {
    int ret = 0;
    for (; head[a] != head[b]; b = par[head[b]]) {
        if (depth[head[a]] > depth[head[b]]) {
            swap(a, b);
        }
        ret = max(ret, seg.query(pos[head[b]], pos[b]));
    }
    if (depth[a] > depth[b]) {
        swap(a, b);
    }
    // if edges shoud be pos[a] + 1
0    ret = max(ret, seg.query(pos[a], pos[b]));
    return ret;
}

int lca(int u,int v) {
    while (head[u] != head[v]) {
        if (depth[head[u]] < depth[head[v]])swap(u, v);
        u = par[head[u]];
    }
    if (depth[u] < depth[v])return u;
    return v;
}

// edges 
 for (auto &[u,v,c]: edges) {
        if (depth[u] < depth[v]) swap(u, v);
        seg.update(pos[u], c);
    }
```