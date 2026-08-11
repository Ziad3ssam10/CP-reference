```cpp
/*

 * making a single non-zero in each row to know the value easily => a * xi = c

 * [2 1 3 | 2] ==> [a1 0 0 | c1]

 * [1 2 3 | 6] ==> [0 0 a2 | c2]

 * [3 2 1 | 10] ==>[0 a3 0 | c3]

 */

bool GaussElimination(vector<vector<int>> a, vector<int>& ans) {

    int n = a.size(), m = (int)a[0].size() - 1;

    vector<int> pos(m, -1);

    // make it all +ve

    for (auto&i : a)

        for (auto&j : i)

            j = (j % mod + mod) % mod;

  

    int det = 1, rank = 0;

    for (int col = 0, row = 0; col < m && row < n; ++col) {

        int mx = row;

        for (int r = row + 1; r < n; ++r) if (a[r][col] > a[mx][col]) mx = r;

        if (a[mx][col] == 0) {det = 0; continue;} // the column is all zeros

  

        // row swap operation, start from col bec all before it is zeros in both rows

        for (int j = col; j <= m; ++j) swap(a[mx][j], a[row][j]);

        if (row != mx) det = det ? mod - det : 0;

        det = mul(det, a[row][col]);

        pos[col] = row; // the column col has this row non-zero, and all the others are zeros

        int inv = fp(a[row][col], mod - 2);

        for (int i = 0; i < n && inv; ++i) {

            if (i == row || a[i][col] == 0) continue; // no swap operation

            // -a[row][col] * r + a[i][col] = 0

            // r = a[i][col]/a[row][col]

            int ratio = mul(a[i][col], inv);

            for (int j = col; j <= m && ratio; ++j)

                a[i][j] = add(a[i][j], -1*mul(ratio, a[row][j]));

        }

        row++, rank++;

    }

    ans = vector<int>(m, 0);

    for (int i = 0; i < m; ++i) {

        if (pos[i] == -1) continue; // free variable => could be anything

        // xi * a[pos[i]][i] = ci (ci is a[pos[i]][m]

        ans[i] = mul(a[pos[i]][m], fp(a[pos[i]][i], mod - 2));

    }

  

    // make sure solution is valid

    for (int i = 0; i < n; ++i) {

        int val = 0;

        for (int j = 0; j < m; ++j)     val = add(val, mul(ans[j], a[i][j]));

        if (val != a[i][m]) return false;

    }

    return true;

}
```