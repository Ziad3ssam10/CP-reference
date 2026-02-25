
### 1. All Pairs Sum

To find the frequency of all possible sums $a_i + a_j$:

- **The Polynomial:** Let $P(x) = \sum freq[a_i] \cdot x^{a_i}$.
    
- **The Operation:** Calculate $R(x) = P(x) \cdot P(x)$.
    
- **The Result:** The coefficient of $x^k$ in $R(x)$ is the number of pairs $(i, j)$ such that $a_i + a_j = k$.
    
- **Note:** If $i \neq j$ is required, subtract the cases where an element is added to itself ($x^{2a_i}$) and divide by 2 for unordered pairs.
    

### 2. All Subarray Sums

A subarray sum $[l, r]$ is defined as $S_r - S_{l-1}$, where $S$ is the prefix sum array.

- **The Polynomial:** This is actually an "All Pairs Difference" problem. Let $P(x) = \sum x^{S_i}$.
    
- **The Operation:** Multiply $P(x)$ by its "reverse," $P(x^{-1})$.
    
- **Handling Negatives:** To avoid negative exponents in FFT, shift the indices: $P_{rev}(x) = \sum x^{MAX\_SUM - S_i}$.
    
- **The Result:** The coefficient of $x^{MAX\_SUM + k}$ gives the count of subarrays summing to $k$.
    
- **Zero Sums:** Count these separately using a frequency map of prefix sums: $\sum \binom{freq[S_i]}{2}$.
    

### 3. All Pairs Multiply with Distance $d = i - j$

When you need to find $\sum a_i \cdot a_j$ where the indices have a specific relationship (like a fixed distance):

- **The Polynomials:** * $A(x) = \sum a_i x^i$
    
    - $B(x) = \sum a_i x^{-i}$ (Reverse of $A$)
        
- **The Result:** The coefficient of $x^d$ in $A(x)B(x)$ corresponds to the sum of products $a_i \cdot a_j$ where $i - j = d$.
    
- **Cyclic Shifts:** To handle cyclic correlations, use **Periodic Convolution**. Double the second array ($A + A$) and multiply with the reversed first array to capture all wrap-around distances.
    

### 4. All Subset Sums (Knapsack)

To find how many ways to form a sum using any subset of elements:

- **The Polynomial:** For each element $v_i$, create $P_i(x) = (1 + x^{v_i})$.
    
- **The Operation:** $R(x) = \prod_{i=1}^n (1 + x^{v_i})$.
    
- **Optimization:** Instead of $n$ FFTs, group identical values. If value $v$ appears $k$ times, the contribution is $(1 + x^v)^k$.
    
- **Advanced:** Use $\ln$ and $\exp$ to turn the product into a sum: $\exp(\sum \ln(1 + x^{v_i}))$. This reduces the complexity to $O(W \log W)$ where $W$ is the maximum possible sum.

### String matching 
 * ret[k] = number of matching characters between pat and s shifted by k
```cpp
vector<int> string_matching(string &s, string &pat) {
    int n = s.size(), m = pat.size();
    vector<int> poly1(n), poly2(m);
    vector<int> ret(n);
    int shift = m - 1;
    for (int i = 0; i < 3; i++) {
        for (int j = 0; j < n; j++) {
            poly1[j] = ((s[j] - 'a') == i);
        }
        for (int j = 0; j < m; j++) {
            poly2[shift - j] = ((pat[j] - 'a') == i);
        }
        auto ans = multiply(poly1, poly2);
        for (int j = m-1; j < n; j++) {
            ret[j - (m-1)] += ans[j];
        }
    }
    return ret;
}

void solve(int tc) {
    string s;
    cin >> s;
    string pat = s;
    for (int i = 0; i < pat.size(); i++)s += '*';
    auto match = string_matching(s, pat);
    int n = s.size(), m = pat.size();
    int maxi = 0;
    for (int i = 1; i <= n - m; i++) {
        maxi = max(maxi, match[i]);
    }
    cout << maxi << endl;
    for (int i = 1; i <= n - m; i++) {
        if (match[i] == maxi)cout << i << " ";
    }
}

```

### wild card 
```cpp
using cd = complex<double>;
const double PI = acos(-1), eps = 5e-4; // If you get a wrong answer you can change the eps lower of higher till you pass

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

vector<cd> multiply(vector<cd> const& a, vector<cd> const& b) {
    vector<cd> fa(a.begin(), a.end()), fb(b.begin(), b.end());
    int n = 1;
    while (n < (int)a.size() + (int)b.size())
        n <<= 1;
    fa.resize(n);
    fb.resize(n);

    fft(fa, false);
    fft(fb, false);
    for (int i = 0; i < n; i++)
        fa[i] *= fb[i];
    fft(fa, true);

    return fa;
}

void solve(int tc) {

    string s, patt; cin >> s >> patt;
    int n = (int)s.length(), m = (int)patt.length();

    vector<cd> poly1(n), poly2(m);

    for (int i = 0; i < n; ++i) {
        double angle = 2*PI*(s[i]-'a')/26;
        poly1[i] = cd(cos(angle), sin(angle));
    }
    for (int i = 0; i < m; ++i) {
        if(patt[m-i-1] == '*') poly2[i] = cd(0,0); // Wild Card
        else {
            double angle = 2*PI*(patt[m-i-1]-'a')/26;
            poly2[i] = cd(cos(angle), -sin(angle));
        }
    }

    vector<cd> ans = multiply(poly1, poly2);
    int wild_cnt = (int)count(patt.begin(), patt.end(), '*');

    int tot = 0;
    vector<int> pos;
    for (int i = 0; i < n; ++i) {
        if(fabs(ans[m-1+i].real() - (m - wild_cnt)) < eps && fabs(ans[m-1+i].imag()) < eps) {
            ++tot;
            pos.push_back(i);
        }
    }

    cout << tot << "\n";
    for(auto & p : pos) cout << p << " ";
    cout << "\n";

}
```

### Excat match
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
    while (n < (int)a.size() + (int)b.size())
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

void solve(int tc) {

    string s, patt; cin >> s >> patt;
    int n = (int)s.length(), m = (int)patt.length();

    vector<int> poly1(n), poly2(m);

    vector<int> ans_match(n);

    for (int i = 0; i < 26; ++i) {
        for (int j = 0; j < n; ++j) {
            poly1[j] = (s[j] - 'a') == i;
        }
        for (int j = 0; j < m; ++j) {
            poly2[j] = (patt[m-j-1] - 'a') == i;
        }
        vector<int> ans = multiply(poly1, poly2);
        for (int j = 0; j < n; ++j) {
            ans_match[j] += ans[m-1+j];
        }
    }


    int tot = 0;
    vector<int> pos;
    int wild_cnt = (int)count(patt.begin(), patt.end(), '*');
    for (int i = 0; i < n; ++i) {
        if(ans_match[i] == m - wild_cnt) {
            ++tot;
            pos.push_back(i);
        }
    }

    cout << tot << "\n";
    for(auto & p : pos) cout << p << " ";
    cout << "\n";

}
```

```cpp
Big int Multiply.txt
string mul_two_big_int(const string &s1, const string &s2) {
    int n = s1.size(), m = s2.size();

    vector<int> poly1(n), poly2(m);
    for (int i = 0; i < n; ++i) {
        poly1[n-i-1] = s1[i] - '0';
    }

    for (int i = 0; i < m; ++i) {
        poly2[m-i-1] = s2[i] - '0';
    }

    vector<int> ans = multiply(poly1, poly2);
    int k = ans.size();

    for (int i = 0; i < k - 1; ++i) {
        ans[i + 1] += ans[i] / 10;
        ans[i] = ans[i] % 10;
    }

    string final = to_string(ans[k - 1]);
    for (int i = k - 2; i >= 0; --i) {
        final += (char)(ans[i] + '0');
    }

    for (int i = 0; i < k; ++i) {
        if(final[i] != '0') return final.substr(i);
    }
    return "0";
}

string power_of_big_int(string s, int p) {
    string ans = "1";
    while (p) {
        if(p&1) ans = mul_two_big_int(ans, s);
        s = mul_two_big_int(s, s);
        p >>= 1;
    }
    return ans;
}```
```