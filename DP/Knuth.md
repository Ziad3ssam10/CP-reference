
<math xmlns="http://www.w3.org/1998/Math/MathML" display="block">
  <mi>d</mi>
  <mi>p</mi>
  <mo stretchy="false">(</mo>
  <mi>i</mi>
  <mo>,</mo>
  <mi>j</mi>
  <mo stretchy="false">)</mo>
  <mo>=</mo>
  <munder>
    <mo data-mjx-texclass="OP" movablelimits="true">min</mo>
    <mrow data-mjx-texclass="ORD">
      <mn>0</mn>
      <mo>&#x2264;</mo>
      <mi>k</mi>
      <mo>&#x2264;</mo>
      <mi>j</mi>
    </mrow>
  </munder>
  <mspace linebreak="newline"></mspace>
  <mrow data-mjx-texclass="ORD">
    <mi>d</mi>
    <mi>p</mi>
    <mo stretchy="false">(</mo>
    <mi>i</mi>
    <mo>&#x2212;</mo>
    <mn>1</mn>
    <mo>,</mo>
    <mi>k</mi>
    <mo>&#x2212;</mo>
    <mn>1</mn>
    <mo stretchy="false">)</mo>
    <mo>+</mo>
    <mi>C</mi>
    <mo stretchy="false">(</mo>
    <mi>k</mi>
    <mo>,</mo>
    <mi>j</mi>
    <mo stretchy="false">)</mo>
    <mspace linebreak="newline"></mspace>
  </mrow>
</math>

 Tested
#### Cp algo 
```cpp
int solve() {
    int N;
    ... // read N and input
    int dp[N][N], opt[N][N];

    auto C = [&](int i, int j) {
        ... // Implement cost function C.
    };

    for (int i = 0; i < N; i++) {
        opt[i][i] = i;
        ... // Initialize dp[i][i] according to the problem
    }

    for (int i = N-2; i >= 0; i--) {
        for (int j = i+1; j < N; j++) {
            int mn = INT_MAX;
            int cost = C(i, j);
            for (int k = opt[i][j-1]; k <= min(j-1, opt[i+1][j]); k++) {
                if (mn >= dp[i][k] + dp[k+1][j] + cost) {
                    opt[i][j] = k; 
                    mn = dp[i][k] + dp[k+1][j] + cost; 
                }
            }
            dp[i][j] = mn; 
        }
    }

    return dp[0][N-1];
}
```

```cpp
const int N = 5000 + 20;
int a[N];
int dp[N][N], opt[N][N];
int n;

void solve(int tc) {
    cin >> n;
    for (int i = 1; i <= n; i++) {
        cin >> a[i];
        a[i] += a[i - 1];
    }
    auto cost = [&](int i, int j) {
        return a[j] - a[i - 1];
    };
    for (int i = 0; i <= n; i++) {
        opt[i][i] = i;
    }
    for (int i = n - 1; i > 0; i--) {
        for (int j = i + 1; j <= n; j++) {
            int mini = 1e18;
            int C = cost(i, j);
            for (int k = opt[i][j - 1]; k <= min(j - 1, opt[i + 1][j]); k++) {
                if (mini >= dp[i][k] + dp[k + 1][j] + C) {
                    opt[i][j] = k;
                    mini = dp[i][k] + dp[k + 1][j] + C;
                }
            }
            dp[i][j] = mini;
        }
    }
    cout << dp[1][n] << endl;
}
```