```cpp
struct SparseTable {
    vector<int> lg;
    vector<vector<int>> table;

    SparseTable(vector<int> &a) {
        int n = a.size();
        table = vector<vector<int>>(n + 5, vector<int>(20));
        lg.resize(n + 5);
        for (int i = 2; i <= n; i++)
            lg[i] = lg[i >> 1] + 1;
        for (int i = 0; i < n; i++)
            table[i][0] = a[i];
        for (int j = 1; j <= lg[n]; j++) {
            for (int i = 0; i + (1 << j) - 1 < n; i++)
                table[i][j] = merge(table[i][j - 1], table[i + (1 << (j - 1))][j - 1]);
        }
    }
    int query(int l, int r) {
        int sz = lg[r - l + 1];
        return merge(table[l][sz], table[r - (1 << sz) + 1][sz]);
    }
    int merge(int a, int b) { return gcd(a, b); }
};
```