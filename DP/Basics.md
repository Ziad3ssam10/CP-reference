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

