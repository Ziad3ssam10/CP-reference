### 2D prefix and partial
```cpp 
//2D prefix sum
template<typename T = ll> struct pref_2D {
    int n, m;
    vector<vector<T>> pref;

    pref_2D(const vector<vector<T>>& x) {
        n = x.size();
        m = x[0].size();
        pref.resize(n, vector<T>(m, 0));

        for (int j = 0; j < m; j++) {
            pref[0][j] = x[0][j];
            for (int i = 1; i < n; i++)
                pref[i][j] = pref[i - 1][j] + x[i][j];
        }
        for (int i = 0; i < n; i++)
            for (int j = 1; j < m; j++)
                pref[i][j] += pref[i][j - 1];
    }
    ll get(int x, int y) {
        if (x < 0 || y < 0) return 0;
        return pref[x][y];
    }
    ll get(int i1, int j1, int i2, int j2) {
        T ret = get(i2, j2) - get(i2, j1 - 1);
        ret -= get(i1 - 1, j2) - get(i1 - 1, j1 - 1);
        return ret;
    }
};
//2D partial sum
struct partial_sum_2D{
    int n, m;
    vector<vector<ll>> upd;
    partial_sum_2D(int _n, int _m) {
        n = n, m = _m;
        upd.resize(n, vector<ll>(m));
    }
    void update(int x1, int y1, int x2, int y2, ll val) {
        upd[x1][y1] += val;
        if (x2 + 1 < n) upd[x2 + 1][y1] -= val;
        if (y2 + 1 < m) upd[x1][y2 + 1] -= val;
        if (x2 + 1 < n and y2 + 1 < m) upd[x2 + 1][y2 + 1] += val;
    }
    void calc() {
        for (int i = 1; i < n; i++)
            for (int j = 0; j < m; j++) upd[i][j] += upd[i - 1][j];

        for (int i = 0; i < n; i++)
            for (int j = 1; j < m; j++) upd[i][j] += upd[i][j - 1];
    }
};

```

### files
```cpp
    freopen("in.txt", "r", stdin);
    freopen("out.txt", "w", stdout);
```


## RNG Setup (C++)

Always use `mt19937` instead of `rand()`. It is faster, has a much larger period ($2^{19937}-1$), and produces higher-quality randomness.

C++

```cpp
#include <bits/stdc++.h>
using namespace std;

// Seed with the current time to ensure different results every run
mt19937 rng(chrono::steady_clock::now().time_since_epoch().count());

// Generate a random 32-bit integer
int x = rng();

// For 64-bit integers, use mt19937_64
mt19937_64 rng64(chrono::steady_clock::now().time_since_epoch().count());
```

## 2. Random Integer in Range $[L, R]$

Using the modulo operator (`%`) can introduce **modulo bias**. The `uniform_int_distribution` is the mathematically correct way to handle ranges.

C++

```cpp
int L = 1, R = 100;
uniform_int_distribution<int> dist(L, R);
int x = dist(rng);

// For long long:
uniform_int_distribution<long long> dist_ll(1LL, 1e18);
long long y = dist_ll(rng);
```

### Mono tonic things

```cpp
#include <bits/stdc++.h>
using namespace std;

struct MonotonicMinDeque {
    deque<int> dq;
    vector<long long> *arr;

    MonotonicMinDeque(vector<long long> &a) {
        arr = &a;
    }

    void push(int i) {
        while (!dq.empty() && (*arr)[dq.back()] >= (*arr)[i])
            dq.pop_back();
        dq.push_back(i);
    }

    void pop(int i) {
        if (!dq.empty() && dq.front() == i)
            dq.pop_front();
    }

    long long get() {
        return (*arr)[dq.front()];
    }

    bool empty() {
        return dq.empty();
    }
};
```

```cpp
// Function to implement monotonic increasing stack
vector<int> monotonicIncreasing(vector<int>& nums)
{
    int n = nums.size();
    stack<int> st;
    vector<int> result;

    // Traverse the array
    for (int i = 0; i < n; ++i) {

        // While stack is not empty AND top of stack is more
        // than the current element
        while (!st.empty() && st.top() > nums[i]) {

            // Pop the top element from the
            // stack
            st.pop();
        }

        // Push the current element into the stack
        st.push(nums[i]);
    }

    // Construct the result array from the stack
    while (!st.empty()) {
        result.insert(result.begin(), st.top());
        st.pop();
    }

    return result;
}

vector<int> nextGreater(vector<int> &a) {
    int n = a.size();
    vector<int> nxt(n, -1);
    stack<int> st;
    for (int i = 0; i < n; i++) {
        while (!st.empty() and a[i] > a[st.top()]) {
            nxt[st.top()] = i;
            st.pop();
        }
        st.push(i);
    }
    return nxt;
}

vector<int> prevGreater(vector<int> &a) {
    int n = a.size();
    vector<int> pre(n, -1);
    stack<int> st;
    for (int i = 0; i < n; i++) {
        while (!st.empty() && a[st.top()] <= a[i])
            st.pop();
        if (!st.empty()) pre[i] = st.top();
        st.push(i);
    }
    return pre;
}

```