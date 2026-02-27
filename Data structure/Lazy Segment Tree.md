```cpp
struct SegmentTree {
    struct Node {
        int f = -1e9;
    };
    Node skip;
    vector<Node> tree;
    vector<int> lazy;
    int n;
    SegmentTree() {}
    SegmentTree(int m, vector<int>& a) {
        n = m;
        tree.resize(n << 2);
        lazy.resize(n << 2, 0);
        skip.f = -1e9; // value to skip when query
        build(1, 0, n - 1, a);
    }

    // base case
    Node single(int x) {
        Node ret;
        ret.f = x;
        return ret;
    }
    Node merge(Node a, Node b) {
        Node ret;
        ret.f = max(a.f,b.f);
        return ret;
    }
    void build(int u, int st, int en, vector<int> &a) {
        if (st == en) {
            tree[u] = single(a[st]);
            return;
        }
        int mid = st + en >> 1;
        build(u << 1, st, mid, a);
        build((u << 1) | 1, mid + 1, en, a);
        tree[u] = merge(tree[u << 1], tree[(u << 1) | 1]);
    }
    void prop(int u, int st, int en) {
        if (!lazy[u]) return;

        // update the node value
        tree[u].f += lazy[u];

        // move the lazy to my children
        if (st != en) {
            lazy[u << 1] += lazy[u];
            lazy[(u << 1) | 1] += lazy[u];
        }

        lazy[u] = 0;
    }
    void update(int u, int st, int en, int idx, int delta) {
        prop(u, st, en);
        if (idx > en || idx < st) return;
        if (st == en) {
            tree[u].f += delta;
            return;
        }
        int mid = st + en >> 1;
        update(u << 1, st, mid, idx, delta);
        update((u << 1) | 1, mid + 1, en, idx, delta);
        tree[u] = merge(tree[u << 1], tree[(u << 1) | 1]);
    }
    void update_range(int u, int st, int en, int lx, int rx, int delta) {
        prop(u, st, en);
        if (st > rx || en < lx) return;
        if (st >= lx && en <= rx) {
            lazy[u] += delta;
            prop(u, st, en);
            return;
        }
        int mid = st + en >> 1;
        update_range(u << 1, st, mid, lx, rx, delta);
        update_range((u << 1) | 1, mid + 1, en, lx, rx, delta);
        tree[u] = merge(tree[u << 1], tree[(u << 1) | 1]);
    }
    Node query(int u, int st, int en, int lx, int rx) {
        prop(u, st, en);
        if (st > rx || en < lx) return skip;
        if (st >= lx && en <= rx) return tree[u];
        int mid = st + en >> 1;
        return merge(query(u << 1, st, mid, lx, rx),
            query((u << 1) | 1, mid + 1, en, lx, rx));
    }

    int query(int lx, int rx) {
        --lx, --rx;
        return query(1, 0, n - 1, lx, rx).f;
    }
    void update(int idx, int delta) {
        --idx;
        update(1, 0, n - 1, idx, delta);
    }
    void update_range(int lx, int rx, int delta) {
        --lx, --rx;
        update_range(1, 0, n - 1, lx, rx, delta);
    }
};
```