```cpp

TEsted
constexpr int mod = 1e9 + 7;  
int add(long long a, long long b) {  
    return (a%mod + b%mod + 2*mod) % mod;  
}  
int mul(long long a, long long b) {  
    return ((a%mod) * (b%mod)) % mod;  
}  
int fp(long long a, long long p) {  
    int ret = 1;  
    while (p) {  
        if (p & 1) ret = mul(ret, a);  
        a = mul(a, a), p >>= 1;  
    }  
    return ret;  
}  
constexpr int P = 998244353, Q = 1e9 + 1, R = 99999989; // any primes  
// f(x) = hash * Q + R  
struct TreeIsomorphism {  
    int n;  
    vector<vector<int>> adj;  
    TreeIsomorphism(int n, vector<vector<int>>& adj) : n(n), adj(adj) {}  
    // each tree has at most 2 centers  
    vector<int> get_centers() {  
        // if rooted return {root};
        vector<int> deg(n + 5, 0);  
        for (int i = 1; i <= n; ++i) deg[i] = adj[i].size();  
  
        queue<int> leafs;  
        for (int i = 1; i <= n; ++i) if (deg[i]<=1) leafs.push(i);  
        int rem = n;  
        while (rem > 2) {  
            int sz = leafs.size();  
            rem -= sz;  
            for (int i = 0; i < sz; ++i) {  
                int u = leafs.front();  
                leafs.pop();  
                for (auto&v : adj[u])  
                    if (--deg[v] == 1) leafs.push(v);  
            }  
        }  
        vector<int> cent;  
        while (!leafs.empty()) {  
            cent.push_back(leafs.front());  
            leafs.pop();  
        }  
        return cent;  
    }  
  
    int get_hash(int u, int p) {  
        int ans = 0;  
        for (auto&v : adj[u])  
            if (v != p)  
                ans = add(ans, fp(P, get_hash(v, u)));  
        // P^tree_hash1 + P^tree_hash2 => multiset hashing  
        return add(mul(ans, Q), R); // to differ the levels from each other  
    }  
  
    bool isomorphism(TreeIsomorphism& tr) {  
        auto v1 = get_centers();  
        auto v2 = tr.get_centers();  
        for (auto&u1 : v1) {  
            for (auto&u2: v2) {  
                if (get_hash(u1, -1) == tr.get_hash(u2, -1))  
                    return true;  
            }  
        }  
        return false;  
    }  
};
```