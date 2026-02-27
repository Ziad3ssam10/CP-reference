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

other 
```cpp

void countsort(vector<int> &p, vector<int> &c) {
    int n = p.size();
    vector<int> cnt(n, 0);
    for (auto x: c)cnt[x]++;
    vector<int> p_new(n);
    vector<int> pos(n);
    pos[0] = 0;
    for (int i = 1; i < n; i++) pos[i] = pos[i - 1] + cnt[i - 1];
    for (auto x: p) {
        int i = c[x];
        p_new[pos[i]] = x;
        pos[i]++;
    }
    p = p_new;
}

vector<int> suffixarray(vector<int> v) {
    vector<int> s = v;
    s.push_back(-2e9);
    int n = s.size();
    vector<int> p(n), c(n);
    {
        vector<pair<int, int> > a(n);
        for (int i = 0; i < n; i++) a[i] = {s[i], i};
        sort(a.begin(), a.end());
        for (int i = 0; i < n; i++) p[i] = a[i].second;
        c[p[0]] = 0;
        for (int i = 1; i < n; i++) {
            if (a[i].first == a[i - 1].first) c[p[i]] = c[p[i - 1]];
            else c[p[i]] = c[p[i - 1]] + 1;
        }
    }

    int k = 0;
    while ((1 << k) < n) {
        for (int i = 0; i < n; i++) p[i] = (p[i] - (1 << k) + n) % n;
        countsort(p, c);
        vector<int> c_new(n);
        c_new[p[0]] = 0;
        for (int i = 1; i < n; i++) {
            pair<int, int> prv = {c[p[i - 1]], c[(p[i - 1] + (1 << k)) % n]};
            pair<int, int> cur = {c[p[i]], c[(p[i] + (1 << k)) % n]};
            if (prv == cur) c_new[p[i]] = c_new[p[i - 1]];
            else c_new[p[i]] = c_new[p[i - 1]] + 1;
        }
        c = c_new;
        k++;
    }
    return p;
}

vector<int> lcp(vector<int> s, vector<int> &p) {
    int n = p.size();
    vector<int> rank(n);
    for (int i = 0; i < n; i++)rank[p[i]] = i;
    int k = 0;
    vector<int> lcp(n - 1);
    for (int i = 0; i < n - 1; i++) {
        if (rank[i] == n - 1) {
            k = 0;
            continue;
        }
        int j = p[rank[i] + 1];
        while (i + k < n - 1 and j + k < n - 1 and s[i + k] == s[j + k])k++;
        lcp[rank[i]] = k;
        if (k)k--;
    }
    return lcp;
}
```