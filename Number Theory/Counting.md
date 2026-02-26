### Pascal
```cpp
// C[n][r] = nCr  
void buildPascal() {  
    for (int i = 0; i <= N; i++) {  
        for (int j = 0; j <= i; j++) {  
            if (j == 0)  
                C[i][j] = 1;  
            else  
                C[i][j] = C[i - 1][j] + C[i - 1][j - 1];  
        }  
    }  
}
```


### nCr any mod , n to 1e18 
```cpp
#include <bits/stdc++.h>  
#include <ext/pb_ds/assoc_container.hpp>  
#include <ext/pb_ds/tree_policy.hpp>  
  
using namespace std;  
using namespace __gnu_pbds;  
template<typename T>  
using orderedset = tree<T, null_type, less<T>, rb_tree_tag, tree_order_statistics_node_update>;  
#define int long long  
#define ll long long  
#define ld long double  
#define endl '\n'  
#define wady                      \  
    ios_base::sync_with_stdio(0); \  
    cin.tie(0);                   \  
    cout.tie(0);  
  
void files() {  
#ifndef ONLINE_JUDGE  
    freopen("in.txt", "r", stdin);  
    freopen("out.txt", "w", stdout);  
#endif  
}  
  
ll fp(ll n, ll p, ll mod) {  
    n = (n % mod + mod) % mod;  
    ll ans = 1;  
    while (p) {  
        if (p & 1) ans = ans * n % mod;  
        n = n * n % mod;  
        p >>= 1;  
    }  
    return ans;  
}  
  
ll extended_euclid(ll a, ll b, ll &x, ll &y) {  
    if (b == 0) {  
        x = 1;  
        y = 0;  
        return a;  
    }  
    ll x1, y1;  
    ll d = extended_euclid(b, a % b, x1, y1);  
    x = y1;  
    y = x1 - y1 * (a / b);  
    return d;  
}  
  
ll inverse(ll a, ll m) {  
    ll x, y;  
    ll g = extended_euclid(a, m, x, y);  
    if (g != 1) return -1;  
    return (x % m + m) % m;  
}  
  
// returns n! % mod without taking all the multiple factors of p into account that appear in the factorial  
// mod = multiple of p  
// O(mod) * log(n)  
ll get_fact_mod(ll n, ll p, ll mod) {  
    ll f[mod + 1];  
    f[0] = 1;  
    for (int i = 1; i <= mod; i++) {  
        if (i % p) f[i] = f[i - 1] * i % mod;  
        else f[i] = f[i - 1];  
    }  
    ll ans = 1;  
    while (n > 1) {  
        ans = ans * f[n % mod] % mod;  
        ans = ans * fp(f[mod], n / mod, mod) % mod;  
        n /= p;  
    }  
    return ans;  
}  
  
ll multiplicity(ll n, ll p) {  
    ll ans = 0;  
    while (n) {  
        n /= p;  
        ans += n;  
    }  
    return ans;  
}  
  
// nCr mod p^k  
// O(p^k log n)  
ll nCr(ll n, ll r, ll p, ll k) {  
    if (n < r || r < 0 || n < 0) return 0;  
    ll mod = 1;  
    for (int i = 0; i < k; ++i)  
        mod *= p;  
    ll t = multiplicity(n, p) - multiplicity(r, p) - multiplicity(n - r, p);  
    if (t >= k) return 0;  
    ll ans = get_fact_mod(n, p, mod) * inverse(get_fact_mod(r, p, mod), mod) % mod;  
    ans = ans * inverse(get_fact_mod(n - r, p, mod), mod) % mod;  
    ans = ans * fp(p, t, mod) % mod;  
    return ans;  
}  
  
pair<ll, ll> CRT(ll a1, ll m1, ll a2, ll m2) {  
    ll g = __gcd(m1, m2);  
    if ((a2 - a1) % g != 0) return {-1, -1}; // No solution  
    ll p, q;  
    extended_euclid(m1 / g, m2 / g, p, q);  
    ll mod = m1 / g * m2;  
    ll x = a1 + (__int128) m1 * ((a2 - a1) / g % (m2 / g) * p % (m2 / g)) % mod;  
    x = (x % mod + mod) % mod;  
    return {x, mod};  
}  
  
const int N = 1e6 + 5; // max mod  
int spf[N];  
  
void pre() {  
    iota(spf, spf + N, 0);  
    for (int i = 2; i * i < N; ++i) {  
        if (spf[i] != i) continue;  
        for (int j = i * i; j < N; j += i)  
            spf[j] = min(spf[j], i);  
    }  
}  
  
// O(mod logn logmod)  
ll nCr(ll n, ll r, ll mod) {  
    if (n < r || r < 0 || n < 0) return 0;  
    pair<ll, ll> res = {0, 1};  
    while (mod > 1) {  
        int p = spf[mod], k = 0, cur = 1;  
        while (mod % p == 0) {  
            k++, cur *= p, mod /= p;  
        }  
        res = CRT(res.first, res.second, nCr(n, r, p, k), cur);  
    }  
    return res.first;  
}  
  
void solve(int tc) {  
    int n, k, m;  
    cin >> n >> k >> m;  
    int boxes = (n + k - 1) / k;  
    int rem = k;  
    if (n % k)rem -= n % k;  
    else rem -= k;  
    cout << boxes << " " << nCr(rem + boxes - 1, boxes - 1, m) << endl;;  
}  
  
  
signed main() {  
    wady  
    files();  
    int t = 1;  
    pre();  
    cin >> t;  
    int tc = 1;  
    while (t--)  
        solve(tc++);  
}
```


# Stars and Bars

### Number of non-negative integer solutions to

$$x1+x2+⋯+xk=nx_1 + x_2 + \dots + x_k = nx1​+x2​+⋯+xk​=n$$

$$\binom{n+k-1}{k-1}$$


$$\sum_{k=r}^{n} \binom{k}{r} = \binom{n+1}{r+1}$$



$$\sum_{k=0}^{n} k \binom{n}{k} = n \cdot 2^{\,n-1}$$


$$\sum_{k=0}^{n} \binom{n}{k}^2 = \binom{2n}{n}$$


$$\sum_{k=0}^{n} (-1)^k \binom{n}{k} = 0 \quad \text{for } n \ge 1$$


$$\sum_{k=0}^{n} \binom{n}{k} = 2^n$$






$$\sum_{k=1}^{n} k^2 = \frac{n(n+1)(2n+1)}{6}$$


$$\sum_{k=1}^{n} k^3 = \left( \frac{n(n+1)}{2} \right)^2$$




