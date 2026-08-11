Tested

```cpp
ll add(ll a, ll b) {
    return (a + b);
}
ll mul(ll a, ll b) {
    return (a * b);
}
// IF THERE IS MOD SPACE DO NOT FORGET THE DIVISION IN THE multiply FUNCTION!!!!!!!!!!!!!!!!!!!!

// Important notes => if size polynomial is 2^i (must be a power of 2)
  // complexity multiplication is O(i * 2^i) === O(n logn)
  // Power polynomial to p complexity is O(n logn logp) NOT LIKE NORMAL FFT
#define AND 0
#define OR 1
#define XOR 2
void fwht(vector<ll> &a, ll inv, ll f) {
    ll sz = a.size(); // sz must be (1 << i)
    for (ll len = 1; 2 * len <= sz; len <<= 1) {
        for (ll i = 0; i < sz; i += 2 * len) {
            for (ll j = 0; j < len; j++) {
                ll x = a[i + j];
                ll y = a[i + j + len];
                if (f == AND) {
                    if (!inv) a[i + j] = y, a[i + j + len] = add(x, y);
                    else a[i + j] = add(y, -x), a[i + j + len] = x;
                } else if (f == OR) {
                    if (!inv) a[i + j + len] = add(x, y);
                    else a[i + j + len] = add(y, -x);
                } else if (f == XOR) {
                    a[i + j] = add(x, y);
                    a[i + j + len] = add(x, -y);
                }
            }
        }
    }
}
vector<ll> multiply(vector<ll> a, vector<ll> b, ll f) {
    ll sz = a.size();
    fwht(a, 0, f);
    fwht(b, 0, f);
    vector<ll> c(sz);
    for (ll i = 0; i < sz; ++i) {
        c[i] = mul(a[i],b[i]);
    }
    fwht(c, 1, f);
    if (f == XOR) {
        for (ll i = 0; i < sz; ++i) {
            c[i] = c[i] / sz;
        }
    }
    return c;
}
```
## Faster 
```cpp
template<int MOD>  
struct FWHT {  
    int fast(int b, int e) {  
        int res = 1;  
        for (; e; e >>= 1, b = 1ll * b * b % MOD)  
            if (e & 1)  
                res = 1ll * res * b % MOD;  
        return res;  
    }  
   
    inline int add(int x, int y) {  
        return x + y - (x + y >= MOD ? MOD : 0);  
    }  
   
    inline int sub(int x, int y) {  
        return x - y + (x - y < 0 ? MOD : 0);  
    }  
   
    void FST(vector<int> &a, bool inv) {  
        for (int n = (int) a.size(), step = 1; step < n; step *= 2) {  
            for (int i = 0; i < n; i += 2 * step)  
                for (int j = i; j < i + step; j++) {  
                    int &u = a[j], &v = a[j + step];  
                    tie(u, v) =  
                            //  inv ? pii(sub(v,u), u) : pii(v, add(u,v)); // AND  
                            //  inv ? pii(v, sub(u,v)) : pii(add(u,v), u); // OR /// include-line                            pair<ll, ll>(add(u, v), sub(u, v)); // XOR /// include-line  
                }  
        }  
        if (inv) {  
            int divisor = fast((int) a.size(), MOD - 2);  
            for (int &x: a) x = 1ll * x * divisor % MOD; // XOR only /// include-line  
        }  
    }  
   
    vector<int> conv(vector<int> a, vector<int> b) {  
        FST(a, 0);  
        FST(b, 0);  
        for (int i = 0; i < (int) a.size(); i++) a[i] = 1ll * a[i] * b[i] % MOD;  
        FST(a, 1);  
        return a;  
    }  
};
```