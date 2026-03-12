### LIS 
```cpp
int lengthOfLIS(ll nums[],int n)
{
    vector<ll> ans;
    ans.push_back(nums[0]);
    for (int i = 1; i < n; i++) {
        if (nums[i] > ans.back()) {
            ans.push_back(nums[i]);
        }
        else {
            int low = lower_bound(ans.begin(), ans.end(),nums[i])- ans.begin();
            ans[low] = nums[i];
        }
    }
    return ans.size();
}



```cpp
 for (int i = 0; i < k; i++) {
        auto [x,y] = a[i];
        int pos = upper_bound(lis.begin(), lis.end(), y) - lis.begin();
        if (pos == lis.size()) {
            lis.push_back(y);
            build.push_back(i);
        } else {
            lis[pos] = y;
            build[pos] = i;
        }
        dp[i] = pos + 1;
        if (pos == 0)par[i] = -1;
        else par[i] = build[pos - 1];
    }
    int maxi = lis.size();
    int lst = -1;
    for (int i = 0; i < k; i++) {
        if (dp[i] == maxi)lst = i;
    }
    vector<int> path;
    while (lst != -1) {
        path.emplace_back(lst);
        lst = par[lst];
    }
```
```

```

### Digit DP 
```cpp
#include <bits/stdc++.h>
#include <ext/pb_ds/assoc_container.hpp>
#include <ext/pb_ds/tree_policy.hpp>

using namespace __gnu_pbds;
using namespace std;

#define double long double
#define int long long
#define ll int
#define MX LLONG_MAX
#define MN LLONG_MIN
#define endl '\n'
#define ordered_set tree<ll, null_type, greater<ll>, rb_tree_tag, tree_order_statistics_node_update>
#define wady ios_base::sync_with_stdio(0); cin.tie(0); cout.tie(0);

int dp[20][2][2][95][95];
string l,r;
int k;
int rec(int idx,int lower ,int upper ,int rem ,int numrem){
    if(idx == r.size())return !(rem % k) and !(numrem);
    int &ret = dp[idx][lower][upper][rem][numrem];
    if(~ret)return ret;
    ret = 0;
    for(int digit = 0;digit < 10;digit++){
        if(!upper and digit > r[idx]-'0')continue;
        if(!lower and digit <l[idx]-'0')continue;
        ret += rec(idx+1,lower | (digit > l[idx]-'0'),upper | (digit < r[idx]-'0'),rem + digit,(numrem*10+digit)%k);
    }
    return ret;
}

void solve(int testcase) {
    cout << "Case " << testcase << ": " ;
    memset(dp,-1,sizeof dp);
    cin >> l >> r >> k;
    while(l.size() < r.size())l = '0'+l;
    if(k >90)return cout << 0 << endl,void();
    cout << rec(0,0,00,00,0) << endl;
}

signed main() {
    wady
    int tt = 1;
#ifndef ONLINE_JUDGE
    freopen("in.txt", "r", stdin);
    freopen("out.txt", "w", stdout);
#endif
    cin >> tt;
    int k = 1;
    while (tt--) solve(k++);
}
```