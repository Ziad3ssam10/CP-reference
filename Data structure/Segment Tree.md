```cpp
struct SegmentTree {
    int n;
    struct Node {
        int val;
    };
    Node skip;
    vector<Node> tree;
    SegmentTree(int n, vector<int>& v) {
        this->n = n;
        tree.resize(n << 2);
        build(1, 0, n - 1, v);
    }
    Node single(int x) {
        Node r;
        r.val = x;
        return r;
    }
    Node merge(Node a, Node b) {
        Node ret;
        ret.val = a.val + b.val;
        return ret;
    }
    void build(int u, int st, int en, vector<int>& a) {
        if (st == en) return void(tree[u] = single(a[st]));
        int mid = st + en >> 1;
        build(u << 1, st, mid, a);
        build((u << 1) | 1, mid + 1, en, a);
        tree[u] = merge(tree[u << 1], tree[(u << 1) | 1]);
    }
    void update(int u, int st, int en, int idx, int val) {
        if (idx > en || idx < st) return;
        if (st == en) return void(tree[u] = single(val));
        int mid = st + en >> 1;
        update(u << 1, st, mid, idx, val);
        update((u << 1) | 1, mid + 1, en, idx, val);
        tree[u] = merge(tree[u << 1], tree[(u << 1) | 1]);
    }
    Node query(int u, int st, int en, int l, int r) {
        if (st >= l && en <= r) return tree[u];
        if (st > r || en < l) return skip;
        int mid = st + en >> 1;
        return merge(query(u << 1, st, mid, l, r),query((u << 1)|1, mid+1, en, l, r));
    }
    void update(int idx, int val) {
        --idx;
        update(1, 0, n - 1, idx, val);
    }
    Node query(int l, int r) {
        --l, --r;
        return query(1, 0, n - 1, l, r);
    }
};
```
### Iterative Segment tree 
```cpp
const int N = 3e5 + 9;

int n, a[N];
struct Node {
    int sum;
 
    Node() { sum = 0; }
    Node(int val) { sum = val; }
};

struct ST {
  int n;
  Node *t;
  ST(int _n) { n = _n; t = new Node[2 * n]; }

  inline Node combine(Node l, Node r) {
    Node res;
    res.sum = l.sum + r.sum;
    return res;
  }

  void build() {
    for(int i = 0; i < n; i++) t[i + n] = Node(a[i+1]);
    for(int i = n - 1; i > 0; --i) t[i] = combine(t[i << 1], t[i << 1 | 1]);
  }

  void upd(int p, int v) {
    p--;
    for (t[p += n] = Node(v); p >>= 1; ) t[p] = combine(t[p << 1], t[p << 1 | 1]);
  }

  Node query(int l, int r) {
    --l;
    bool f1 = 1, f2 = 1;
    Node resl, resr;
    for(l += n, r += n; l < r; l >>= 1, r >>= 1) {
      if(l & 1) resl = f1 ? t[l++] : combine(resl, t[l++]), f1 = 0;
      if(r & 1) resr = f2 ? t[--r] : combine(t[--r], resr), f2 = 0;
    }
    if(f2) return resl;
    if(f1) return resr;
    return combine(resl, resr);
  }
};
```