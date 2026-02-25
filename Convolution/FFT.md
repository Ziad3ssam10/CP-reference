```cpp
using cd = complex<double>;
const double PI = acos(-1);

void fft(vector<cd> & a, bool invert) {
    int n = a.size();

    for (int i = 1, j = 0; i < n; i++) {
        int bit = n >> 1;
        for (; j & bit; bit >>= 1)
            j ^= bit;
        j ^= bit;

        if (i < j)
            swap(a[i], a[j]);
    }

    for (int len = 2; len <= n; len <<= 1) {
        double ang = 2 * PI / len * (invert ? -1 : 1);
        cd wlen(cos(ang), sin(ang));
        for (int i = 0; i < n; i += len) {
            cd w(1);
            for (int j = 0; j < len / 2; j++) {
                cd u = a[i+j], v = a[i+j+len/2] * w;
                a[i+j] = u + v;
                a[i+j+len/2] = u - v;
                w *= wlen;
            }
        }
    }

    if (invert) {
        for (cd & x : a)
            x /= n;
    }
}

vector<int> multiply(vector<int> const& a, vector<int> const& b) {
    vector<cd> fa(a.begin(), a.end()), fb(b.begin(), b.end());
    int n = 1;
    while (n < (int) a.size() + b.size() + 1)
        n <<= 1;
    fa.resize(n);
    fb.resize(n);

    fft(fa, false);
    fft(fb, false);
    for (int i = 0; i < n; i++)
        fa[i] *= fb[i];
    fft(fa, true);

    vector<int> result(n);
    for (int i = 0; i < n; i++)
        result[i] = round(fa[i].real());
    return result;
}
```

### FFT with mod 
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