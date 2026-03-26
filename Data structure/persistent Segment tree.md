```cpp
// don't forget to Build()
// modify update & query for your problem
struct Node {
    int s;
    Node *l = NULL, *r = NULL;
    Node(int s = 0, Node* l = NULL, Node *r = NULL) : s(s), l(l), r(r) {}
};
Node* L(Node *node) { return node->l;}
Node* R(Node *node) { return node->r;}
int S(Node *node) { return node->s;}

Node *leaf(int s) { return new Node(s); }
Node *merge(Node *l, Node *r) {
    Node *ret = new Node();
    ret->l = l, ret->r = r;
    ret->s = S(l) + S(r);
    return ret;
}
Node *build(int l, int r) {
    if (l == r) return leaf(0);
    int md = (l + r) / 2;
    return merge(build(l, md), build(md + 1, r));
}
Node *update(Node* node, int l, int r, int idx, int s) {
    if (l == r) return leaf(s + node->s);
    int md = (l + r) / 2;
    if (idx <= md) return merge(update(L(node), l, md, idx, s), R(node));
    return merge(L(node), update(R(node), md + 1, r, idx, s));
}
int query(Node *node, int l, int r, int s, int e) {
    if (l > e || r < s) return 0;
    if (l >= s && r <= e) return S(node);
    int md = (l + r) / 2;
    return query(L(node), l, md, s, e) + query(R(node), md + 1, r, s, e);
}
```

### implicit 
```cpp
// modify update & query for your problem
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
#define rep(i , st , ed) for(int i = st; i < ed; i++)
#define f first
#define s second
#define all(v) v.begin() , v.end()
#ifndef ONLINE_JUDGE 
#define debug(x) cerr << #x << ": " << x << '\n';
#else
#define debug(x)
#endif
const int N = 1e9+1;

struct Node{
    Node* l, *r;
    ll s;
    Node(ll s = 0, Node* l = NULL, Node* r = NULL):
        s(s) , l(l) , r(r){} 
};

Node* getL(Node* x){ return x == NULL ? x : x->l; }
Node* getR(Node* x){ return x == NULL ? x : x->r; }
ll    getS(Node* x){ return x == NULL ? 0 : x->s; }
Node* newLeaf(int val){ return new Node(val); }

Node* newPar(Node* l, Node* r){
    Node* res = new Node();
    res->l = l; res->r = r;
    res->s = getS(l) + getS(r);
    return res;
} 



int n; // n -> length of array
Node* upd(int i , int v , Node* x , int l = -N , int r = N){
    if(r - l == 1) return newLeaf( v + getS(x) );
    int m = (l+r)/2;
    if(i < m){
        return newPar(upd(i,v,getL(x),l,m), getR(x));
    }else{
        return newPar(getL(x) , upd(i,v,getR(x),m,r));
    }
}

ll qry(int lx , int rx , Node* cur, Node* prv , int l = -N , int r = N){
    if(l >= lx && r <= rx) return (getS(cur) - getS(prv));
    if(r <= lx || l >= rx) return 0;
    int m = (l+r)/2;
    return qry(lx,rx,getL(cur), getL(prv) ,l,m) + qry(lx,rx,getR(cur), getR(prv),m,r);
}

int kth(int k , Node*prv , Node* cur, int l = -N , int r = N){
    if(r-l==1) return l;
    int m = (l+r)/2;

    int sumL = getS( getL(cur) ) - getS( getL(prv) );
    if(sumL >= k) return kth(k , getL(prv) , getL(cur) , l , m);
    return kth(k-sumL , getR(prv) , getR(cur) , m , r);
}

int main(){
    ios::sync_with_stdio(0); cin.tie(NULL); cout.tie(0);
    #ifndef ONLINE_JUDGE
    freopen("in.txt", "r", stdin);
    freopen("out.txt", "w", stdout);
    freopen("error.txt", "w", stderr);
    #endif

    int n , q; cin >> n >> q;
    vector<Node* > versions;

    versions.emplace_back(nullptr);
    for(int i = 1; i <= n; ++i){
        int x; cin >> x;
        versions.emplace_back( upd(x,+1 , versions.back()) );
    }

    while(q--){
        int i,j,k; cin >> i >> j >> k;
        cout << kth(k , versions[i-1] , versions[j]) << '\n';
    }
}
}
```