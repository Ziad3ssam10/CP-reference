
```cpp
const int N = 1e6 + 10, SQ = 175;
int n, q, ans, k;
int a[N], frq[N];

struct query {
    int l, r, i;

    bool operator<(query q) {
        int b1 = l / SQ, b2 = q.l / SQ;
        if (b1 != b2) return b1 < b2;
        return (b1 & 1) ? (r < q.r) : (r > q.r);
    }
};

void add(int idx) {
    frq[a[idx]]++;
    if(frq[a[idx]] == 1)ans++;
}

void rem(int idx) {
    frq[a[idx]]--;
    if(!frq[a[idx]])ans--;
}

vector<int> MOalgo(vector<query> qq) {
    vector<int> ret(q);
    int l = 0, r = -1;

    for (auto &[ql, qr, qi]: qq) {
        while (r < qr) add(++r);
        while (l > ql) add(--l);
        while (r > qr) rem(r--);
        while (l < ql) rem(l++);
        ret[qi] = ans;
    }
    return ret;
}

const int Sq = 450, N = 2e5 + 10;
vector<int> a(N), buc(N / Sq + 10);
int n, q;

void build(int idx) {
    buc[idx] = 0;
    for (int i = idx * Sq; i < min(n, (idx + 1) * Sq); i++) {
        buc[idx] += a[i];
    }
}

int query(int l, int r) {
    int ret = 0;
    for (int i = l; i <= r;) {
        if (i % Sq == 0 and i + Sq - 1 <= r) {
            ret += buc[i / Sq];
            i += Sq;
        } else {
            ret += a[i];
            i++;
        }
    }
    return ret;
}

```