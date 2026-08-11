 ```cpp
const int N = 1e5 + 20;  
int par[N], depth[N], heavy[N], head[N], pos[N];  
int cur_pos = 0;  
vector<int> adj[N];  
int n, a[N];  
   
struct node {  
    int ans = -1e10;  
    int maxpre = -1e10, maxsuf = -1e10;  
    int tot = 0;  
   
    node() {  
    }  
   
    node(int x) {  
        ans = x;  
        tot = x;  
        maxpre = x, maxsuf = x;  
    }  
} skip;  
   
node reversenode(node x) {  
    swap(x.maxpre, x.maxsuf);  
    return x;  
}  
   
node merge(node l, node r) {  
    node ret = node();  
    ret.ans = max(l.ans, r.ans);  
    ret.ans = max(ret.ans, l.maxsuf + r.maxpre);  
    ret.maxpre = max(l.maxpre, l.tot + r.maxpre);  
    ret.maxsuf = max(r.maxsuf, r.tot + l.maxsuf);  
    ret.tot = l.tot + r.tot;  
    return ret;  
}  
   
   
struct Segmenttree {  
    int sz;  
    vector<node> tree;  
   
    Segmenttree() {  
    }  
   
    Segmenttree(int n) {  
        sz = n;  
        tree.assign(sz << 2, {});  
    }  
   
    node single(int x) {  
        node ret;  
        ret = node(x);  
        return ret;  
    }  
   
    void update(int u,int st,int en,int idx,int val) {  
        if (idx > en or idx < st)return;  
        if (st == en)return void(tree[u] = single(val));  
        int mid = (st + en) >> 1;  
        update(u << 1, st, mid, idx, val);  
        update(u << 1 | 1, mid + 1, en, idx, val);  
        tree[u] = merge(tree[u << 1], tree[u << 1 | 1]);  
    }  
   
    node query(int u,int st,int en,int l,int r) {  
        if (st >= l and en <= r)return tree[u];  
        if (st > r or en < l)return skip;  
        int mid = (st + en) >> 1;  
        return merge(query(u << 1, st, mid, l, r), query(u << 1 | 1, mid + 1, en, l, r));  
    }  
   
    void update(int idx,int val) {  
        update(1, 0, n - 1, idx, val);  
    }  
   
    node query(int l,int r) {  
        return query(1, 0, n - 1, l, r);  
    }  
} seg;  
   
   
int dfs(int node) {  
    int sz = 1;  
    int maxch = 0;  
    for (auto child: adj[node]) {  
        if (child == par[node])continue;  
        par[child] = node;  
        depth[child] = depth[node] + 1;  
        int chsz = dfs(child);  
        sz += chsz;  
        if (chsz > maxch) {  
            maxch = chsz;  
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
    dfs(1);  
    decompse(1, 1);  
}  
   
node query(int u,int v) {  
    node left = skip;  
    node right = skip;  
    while (head[u] != head[v]) {  
        if (depth[head[u]] > depth[head[v]]) {  
            node cur = seg.query(pos[head[u]], pos[u]);  
            cur = reversenode(cur);  
            left = merge(left, cur);  
            u = par[head[u]];  
        } else {  
            node cur = seg.query(pos[head[v]], pos[v]);  
            right = merge(cur, right);  
            v = par[head[v]];  
        }  
    }    
    // if edges shoud be pos[a] + 1
    if (depth[u] > depth[v]) {  
        node cur = seg.query(pos[v], pos[u]);  
        cur = reversenode(cur);  
        left = merge(left, cur);  
    } else {  
        node cur = seg.query(pos[u], pos[v]);  
        right = merge(cur, right);  
    }  
    return merge(left, right);  
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