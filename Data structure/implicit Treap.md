```cpp

mt19937 rng(chrono::steady_clock::now().time_since_epoch().count());
// 1 based
struct Node {  
    Node *l, *r;  
    int val, sum;  
    int lazy_add;  
    bool rev;  
    int sz;  
    unsigned int pri;  
  
    Node(int v) : l(nullptr), r(nullptr), val(v), sum(v),  
                  lazy_add(0), rev(false), sz(1), pri(rng()) {  
    }  
};  
  
Node *root = nullptr;  
  
inline int get_sz(Node *t) {  
    return t ? t->sz : 0;  
}  
  
inline int get_sum(Node *t) {  
    return t ? t->sum : 0LL;  
}  
  
void update(Node *t) {  
    if (!t) return;  
    t->sz = 1 + get_sz(t->l) + get_sz(t->r);  
    t->sum = t->val + get_sum(t->l) + get_sum(t->r);  
}  
  
void apply_rev(Node *t) {  
    if (!t) return;  
    swap(t->l, t->r);  
    t->rev ^= 1;  
}  
  
void apply_add(Node *t, int val) {  
    if (!t) return;  
    t->val += val;  
    t->sum += val * t->sz;  
    t->lazy_add += val;  
}  
  
void push(Node *t) {  
    if (!t) return;  
    if (t->rev) {  
        apply_rev(t->l);  
        apply_rev(t->r);  
        t->rev = false;  
    }  
    if (t->lazy_add != 0) {  
        apply_add(t->l, t->lazy_add);  
        apply_add(t->r, t->lazy_add);  
        t->lazy_add = 0;  
    }  
}  
  
void split(Node *t, int k, Node *&x, Node *&y) {  
    if (!t) {  
        x = y = nullptr;  
        return;  
    }  
    push(t);  
    if (get_sz(t->l) >= k) {  
        y = t;  
        split(t->l, k, x, y->l);  
    } else {  
        x = t;  
        split(t->r, k - get_sz(t->l) - 1, x->r, y);  
    }  
    update(t);  
}  
  
Node *merge(Node *x, Node *y) {  
    if (!x || !y) return x ? x : y;  
    push(x);  
    push(y);  
    if (x->pri > y->pri) {  
        x->r = merge(x->r, y);  
        update(x);  
        return x;  
    } else {  
        y->l = merge(x, y->l);  
        update(y);  
        return y;  
    }  
}  
  
long long range_sum(int L, int R) {  
    Node *A, *B, *C;  
    split(root, L - 1, A, B);  
    split(B, R - L + 1, B, C);  
  
    long long ans = get_sum(B);  
  
    root = merge(merge(A, B), C);  
    return ans;  
}  
  
void range_reverse(int L, int R) {  
    Node *A, *B, *C;  
    split(root, L - 1, A, B);  
    split(B, R - L + 1, B, C);  
  
    apply_rev(B);  
  
    root = merge(merge(A, B), C);  
}  
  
void range_add(int L, int R, long long val) {  
    Node *A, *B, *C;  
    split(root, L - 1, A, B);  
    split(B, R - L + 1, B, C);  
  
    apply_add(B, val);  
  
    root = merge(merge(A, B), C);  
}  
  
void insert_at_pos(int pos, long long val) {  
    if (pos < 1 || pos > get_sz(root) + 1) return;  
    Node *A, *B;  
    split(root, pos - 1, A, B);  
    Node *new_node = new Node(val);  
    root = merge(merge(A, new_node), B);  
}  
  
void delete_at_pos(int pos) {  
    if (pos < 1 || pos > get_sz(root)) return;  
    Node *A, *B, *to_delete;  
    split(root, pos - 1, A, B);  
    split(B, 1, to_delete, B);  
    delete to_delete;  
    root = merge(A, B);  
}  
  
void print_array(Node *t) {  
    if (!t) return;  
    push(t);  
    print_array(t->l);  
    cout << t->val << " ";  
    print_array(t->r);  
}  
  
  
void clear(Node * &t) {  
    if (!t) return;  
  
    clear(t->l);  
    clear(t->r);  
  
    delete t;  
    t = nullptr;  
}  
 
```