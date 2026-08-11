```cpp
const int mod = 1e9 + 7;
 
int add(ll a, int b) {
    return (a + b) % mod;
}
 
int mul(ll a, int b) {
    return (a * b) % mod;
}
 
struct Matrix {
    vector<vector<int>> mat;
    int n, m;
 
    Matrix(int _n, int _m, int val = 0) {
        n = _n, m = _m;
        mat = vector<vector<int>>(n, vector<int>(m, val));
    }
 
 
    Matrix operator*(const Matrix &other) const {
        int n = mat.size();
        int m = other.mat[0].size();
        int k = mat[0].size();
        assert(mat[0].size() == other.mat.size());
        Matrix ret(n, m);
        for (int i = 0; i < n; i++)
            for (int j = 0; j < m; j++)
                for (int l = 0; l < k; l++)
                    ret.mat[i][j] = add(ret.mat[i][j], mul(mat[i][l], other.mat[l][j]));
        return ret;
    }
};
 
Matrix I(int n) {
    Matrix ret(n, n);
    for (int i = 0; i < n; i++)ret.mat[i][i] = 1;
    return ret;
}
 
Matrix mult(Matrix &a, Matrix &b) {
    int n = a.mat.size();
    int m = b.mat[0].size();
    int k = a.mat[0].size();
    assert(a.mat[0].size() == b.mat.size());
    Matrix ret(n, m);
    for (int i = 0; i < n; i++)
        for (int j = 0; j < m; j++)
            for (int l = 0; l < k; l++)
                ret.mat[i][j] = add(ret.mat[i][j], mul(a.mat[i][l], b.mat[l][j]));
    return ret;
}
 
Matrix fast_power(Matrix base, int power) {
    Matrix ret = I(base.n);
    while (power > 0) {
        if (power & 1) ret = ret * base;
        base = base * base;
        power >>= 1;
    }
    return ret;
}
```