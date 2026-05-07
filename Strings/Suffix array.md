```cpp
// NLog(N) , 1->Based
struct SuffixArray {
    int n;
// p -> Index Of Sorted Suffixes (0->Based)
// p[0] = index of the smallest suffix -> $
// p[1] = index of the second smallest suffix
// c -> Class Of Each Suffix (For Comparing) : Idx Of Suffix In P
// lcp -> Size of max prefix match with suffix (i - 1)
    vector<int> p, c, lcp;
    string s;

    SuffixArray(string s) {
        s += '\0', this->s = s, n = s.size();
        p = c = lcp = vector<int>(n);
        for (int i = 0; i < n; i++) p[i] = i;
        sort(p.begin(), p.end(), [&](int a, int b) { return s[a] < s[b]; }); // if descending >
        for (int i = 1; i < n; i++)
            c[p[i]] = c[p[i - 1]] + (s[p[i]] != s[p[i - 1]]);
        int k = 0;
        while ((1 << k) < n) {
            for (int i = 0; i < n; i++)
                p[i] = (p[i] - (1 << k) + n) % n;
            vector<int> idx(n), nc(n), temp = p;
            for (auto x: c) idx[x]++;
            for (int i = 1; i < n; i++) idx[i] += idx[i - 1];
            for (int i = n - 1; i >= 0; i--)
                p[--idx[c[temp[i]]]] = temp[i];
            for (int i = 1; i < n; i++)
                nc[p[i]] = nc[p[i - 1]] +
                           (make_pair(c[p[i]], c[(p[i] + (1 << k)) % n]) !=
                            make_pair(c[p[i - 1]], c[(p[i - 1] + (1 << k)) % n]));
            c = nc;
            k++;
        }
        k = 0;
        for (int i = 0; i < n - 1; i++) {
            while (s[i + k] == s[p[c[i] - 1] + k]) k++;
            lcp[c[i]] = k, k = max(k - 1, 0);
        }
    }

    string getKthSuffix(int k) {
        return s.substr(p[k], s.size() - p[k] - 1) + '$';
    }
};
```

other (faster)
```cpp

struct SuffixArray {  
    // suff is the suffix array with the empty suffix being suff[0]  
    // lcp[i] holds the lcp between sa[i], sa[i - 1]    int n;  
    vector<int> suff, lcp, pos, lg;  
    vector<array<int, 21> > table;  
    vector<int> c;  
  
    SuffixArray(string &s, int lim = 256) {  
        n = s.size() + 1;  
        int k = 0, a, b;  
        c.assign(s.begin(), s.end());  
        c.resize(n * 2, 0);  
        vector<int> tmp(n), frq(max(n, lim));  
        c.back() = 0;  
        suff.resize(n); lcp.resize(n); pos.resize(n);  
        iota(suff.begin(), suff.end(), 0);  
        for (int j = 0, p = 0; p < n; j = max(1, j * 2), lim = p) {  
            p = j, iota(tmp.begin(), tmp.end(), n - j);  
            for (int i = 0; i < n; i++)  
                if (suff[i] >= j) tmp[p++] = suff[i] - j;  
  
            fill(frq.begin(), frq.end(), 0);  
            for (int i = 0; i < n; i++) frq[c[i]]++;  
            for (int i = 1; i < lim; i++) frq[i] += frq[i - 1];  
            for (int i = n; i--;) suff[--frq[c[tmp[i]]]] = tmp[i];  
  
            swap(c, tmp), p = 1, c[suff[0]] = 0;  
            for (int i = 1; i < n; i++) {  
                a = suff[i - 1], b = suff[i];  
                c[b] = tmp[a] == tmp[b] && tmp[a + j] == tmp[b + j] ? p - 1 : p++;  
            }  
        }  
  
        for (int i = 1; i < n; i++) pos[suff[i]] = i;  
        for (int i = 0, j; i < n - 1; lcp[pos[i++]] = k)  
            for (k && k--, j = suff[pos[i] - 1]; s[i + k] == s[j + k]; k++) {  
            }  
    }  
  
    void preLcp() {  
        lg.resize(n + 5);  
        table.resize(n + 5);  
        for (int i = 2; i < n + 5; ++i) lg[i] = lg[i / 2] + 1;  
        for (int i = 0; i < n; ++i) table[i][0] = lcp[i];  
        for (int j = 1; j <= lg[n]; ++j)  
            for (int i = 0; i <= n - (1 << j); ++i)  
                table[i][j] = min(table[i][j - 1], table[i + (1 << (j - 1))][j - 1]);  
    }  
  
    // pass the pos of the suffixes  
    int queryLcp(int i, int j) {  
        if (i == j) return n - suff[i] - 1;  
        if (i > j) swap(i, j);  
        i++;  
        int len = lg[j - i + 1];  
        return min(table[i][len], table[j - (1 << len) + 1][len]);  
    }  
};
```