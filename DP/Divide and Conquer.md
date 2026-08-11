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
  
	const int N = 1e5 + 20, K = 22;  
	int dp[K][N];  
	int a[N];  
	int ans = 0;  
	int frq[N];  
	  
	void add(int idx) {  
	    int cont = (frq[a[idx]] * (frq[a[idx]] - 1)) / 2;  
	    ans -= cont;  
	    frq[a[idx]]++;  
	    cont = (frq[a[idx]] * (frq[a[idx]] - 1)) / 2;  
	    ans += cont;  
	}  
	  
	void rem(int idx) {  
	    int cont = (frq[a[idx]] * (frq[a[idx]] - 1)) / 2;  
	    ans -= cont;  
	    frq[a[idx]]--;  
	    cont = (frq[a[idx]] * (frq[a[idx]] - 1)) / 2;  
	    ans += cont;  
	}  
	  
	int n, k;  
	int ql = 1, qr = 0;  
	  
	int query(int l, int r) {  
	    while (ql > l) add(--ql);  
	    while (qr < r) add(++qr);  
	    while (ql < l) rem(ql++);  
	    while (qr > r) rem(qr--);  
	    return ans;  
	}  
	  
	void rec(int i, int l, int r, int optl, int optr) {  
	    if (l > r) return;  
	    int mid = (l + r) >> 1;  
	    int best = optl;  
	    int mini = 1e16;  
	    for (int j = optl; j <= min(mid - 1, optr); j++) {  
	        int cost = query(j + 1, mid);  
	        int val = dp[i - 1][j] + cost;  
	        if (val < mini) {  
	            mini = val;  
	            best = j;  
	        }  
	    }  
	    dp[i][mid] = mini;  
	    rec(i, l, mid - 1, optl, best);  
	    rec(i, mid + 1, r, best, optr);  
	}  
	  
	void solve(int tc) {  
	    cin >> n >> k;  
	    for (int i = 1; i <= n; i++) cin >> a[i];  
	    for (int j = 0; j <= n; j++) dp[0][j] = 1e16;  
	    dp[0][0] = 0;  
	    for (int i = 1; i <= k; i++) rec(i, 1, n, 0, n - 1);  
	    cout << dp[k][n] << endl;  
	}  `
  
  
signed main() {  
    wady  
    files();  
    int t = 1;  
    int tc = 1;  
    while (t--)  
        solve(tc++);  
}
```

#### Cp algo generic implementation
```cpp
int m, n;
vector<long long> dp_before, dp_cur;

long long C(int i, int j);

// compute dp_cur[l], ... dp_cur[r] (inclusive)
void compute(int l, int r, int optl, int optr) {
    if (l > r)
        return;

    int mid = (l + r) >> 1;
    pair<long long, int> best = {LLONG_MAX, -1};

    for (int k = optl; k <= min(mid, optr); k++) {
        best = min(best, {(k ? dp_before[k - 1] : 0) + C(k, mid), k});
    }

    dp_cur[mid] = best.first;
    int opt = best.second;

    compute(l, mid - 1, optl, opt);
    compute(mid + 1, r, opt, optr);
}

long long solve() {
    dp_before.assign(n,0);
    dp_cur.assign(n,0);

    for (int i = 0; i < n; i++)
        dp_before[i] = C(0, i);

    for (int i = 1; i < m; i++) {
        compute(0, n - 1, 0, n - 1);
        dp_before = dp_cur;
    }

    return dp_before[n - 1];
}
```