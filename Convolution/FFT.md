Tested 
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
typedef complex<double> C;

void fft(vector<C> &a) {

    int n = (int)a.size(), L = 31 - __builtin_clz(n);

    static vector<complex<long double>> R(2, C(1., 0));

    static vector<C> rt(2, C(1.0, 0));  // (^ 10% faster if double)

    for (static int k = 2; k < n; k *= 2) {

        R.resize(n);

        rt.resize(n);

        auto x = polar(1.0L, acos(-1.0L) / k);

        for (int i = k; i < 2 * k; i++) rt[i] = R[i] = i & 1 ? R[i / 2] * x : R[i / 2];

    }

    vector<ll> rev(n);

    for (int i = 0; i < n; i++) rev[i] = (rev[i / 2] | (i & 1) << L) / 2;

    for (int i = 0; i < n; i++) if (i < rev[i]) swap(a[i], a[rev[i]]);

    for (int k = 1; k < n; k *= 2)

        for (int i = 0; i < n; i += 2 * k)

            for (int j = 0; j < k; j++) {

                // C z = rt[j+k] * a[i+j+k]; // (25% faster if hand-rolled)

                auto x = (double *) &rt[j + k], y = (double *) &a[i + j + k];

                C z(x[0] * y[0] - x[1] * y[1], x[0] * y[1] + x[1] * y[0]);

                a[i + j + k] = a[i + j] - z;

                a[i + j] += z;

            }

}

  

template<int M>

vector<ll> multiply(const vector<ll> &a, const vector<ll> &b) {

    if (a.empty() || b.empty()) return {};

    vector<ll> res((int)a.size() + (int)b.size() - 1);

    int B = 32 - __builtin_clz((int)res.size()), n = 1 << B, cut = (int) sqrt(M);

    vector<C> L(n), R(n), outs(n), outl(n);

    for (int i = 0; i < (int)a.size(); i++) L[i] = C((int)a[i] / cut, (int)a[i] % cut);

    for (int i = 0; i < (int)b.size(); i++) R[i] = C((int)b[i] / cut, (int)b[i] % cut);

    fft(L), fft(R);

    for (int i = 0; i < n; i++) {

        int j = -i & (n - 1);

        outl[j] = (L[i] + conj(L[j])) * R[i] / (2.0 * n);

        outs[j] = (L[i] - conj(L[j])) * R[i] / (2.0 * n) / 1i;

    }

    fft(outl), fft(outs);

    for (int i = 0; i < (int)res.size(); i++) {

        ll av = (ll)(real(outl[i]) + .5), cv = (ll)(imag(outs[i]) + .5);

        ll bv = (ll)(imag(outl[i]) + .5) + (ll)(real(outs[i]) + .5);

        res[i] = ((av % M * cut + bv) % M * cut + cv) % M;

    }

    return res;

}
```