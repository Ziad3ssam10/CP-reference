
### **The Type of Problem It Solves**

Alien's Trick is specifically designed to solve optimization problems that have **all three** of the following characteristics:

1. **Optimization:** You need to find the maximum or minimum possible score, cost, or profit.
2. **The "Exactly $K$" Constraint:** You are forced to choose exactly $K$ items, perform exactly $K$ operations, or divide an array into exactly $K$ segments.
3. **Convexity / Concavity (Diminishing Returns):** If you were to plot the optimal answer for $k=1$, $k=2$, $k=3$, etc., the resulting graph would be a convex or concave curve. This usually means the problem has "diminishing returns" (e.g., the first segment you pick gives you a huge profit, the second gives you a bit less, the third even less, and so on).

## The Aliens Trick: TL;DR Summary

1. **Drop the K Limit:** Ignore the rule that says you must use exactly K items/segments.
2. **Add a Penalty:** Pretend you can use infinite items, but you must pay a fixed penalty (`lamda`) for every single one you use.
3. **Solve the Easy Version:** Write a fast DP to find the maximum score minus the penalties used.
4. **Binary Search the Penalty:** Keep adjusting `lamda` until your DP naturally decides that K items is the perfect amount to use.
5. **Refund the Cost:** Take your final DP score and add back `lamda * K` to get your real answer.

## The 3 Conditions to Use It (Cheat-Sheet)

Check these three boxes before you try to use the trick:

- **The K Bottleneck:** The problem asks you to optimize something using exactly/at most K operations, segments, or edges.
- **Easy Without K:** If the K constraint didn't exist, you could solve the problem easily with a standard greedy or 1D DP approach.
- **Diminishing Returns (Concavity):** The first item you pick gives you the biggest boost. The second gives a little less. The third gives even less. Because the benefits drop off, a uniform penalty will perfectly control how many items you take.
```cpp
int const N = 3e5 + 5;
pair<int, int> dp[N][2];
int vis[N][2], id;
int n, k, a[N];
int lamda;
pair<int, int> rec(int i, bool lst) {
    if (i == n) return {0, 0};
    auto &ret = dp[i][lst];
    if (vis[i][lst] == id) return ret;
    vis[i][lst] = id;
    auto leave = rec(i + 1, 0);
    auto take = rec(i + 1, 1);
    take.first += a[i];
    if (!lst) {
        take.first -= lamda;
        take.second--;
    }

    return ret = max(leave, take);
}

void solve() {
    cin >> n >> k;

    for (int i = 0; i < n; ++i) cin >> a[i];

    // st = 0       "AT MOST K" (empty allowed).
    // st = -1e15   "EXACTLY K" (must force bad choices).
    // en = 1e15     Always. It forces the DP to pick 0 items.
    
    int st = 0, en = 1e15, md; 
    __int128_t ans = 0;
    while (st <= en) {
        md = st + (en - st) / 2;
        lamda = md;
        
        ++id;
        auto [cur_ans, cnt] = rec(0, 0); 
        
        int used = -cnt; // cnt that you take (neg in dp)
        
        if (used > k) {
            // We used too many items. The penalty is too weak.
            st = md + 1;    
        } else {
            // We used <= k items. This is a valid configuration!
            // Refund the penalty for exactly K items to get the true score.
            ans = cur_ans + (__int128_t)md * k; 
            
            // Try to find a smaller valid penalty to handle collinear points
            en = md - 1; 
        }
    }

    cout << (int)ans << '\n';
}
```