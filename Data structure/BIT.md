```cpp
struct FenwickTree {  
    vector<int> bit;  
    int n;  
    int LOGN;  
    // 1 based 
    FenwickTree(int sz) {  
        n = sz + 1;  
        LOGN = 0;  
        while ((1 << LOGN) <= n) LOGN++;  
        bit.assign(n, 0);  
    }  
  
    void add(int idx, int delta) {  
        for (; idx < n; idx += idx & -idx)  
            bit[idx] += delta;  
    }  
  
    int sum(int idx) {  
        int ret = 0;  
        for (; idx > 0; idx -= idx & -idx)  
            ret += bit[idx];  
        return ret;  
    }  
  
    int sum(int l, int r) {  
        if (l > r) return 0;  
        return sum(r) - sum(l - 1);  
    }  
  
    // Find the k-th order statistic (requires a 1-based tree)  
    // Returns the 1-based index of the largest value less than or equal to v    int bit_search(int v) {  
        int sum = 0;  
        int pos = 0;  
  
        for (int i = LOGN; i >= 0; i--) {  
            if (pos + (1 << i) < n && sum + bit[pos + (1 << i)] < v) {  
                sum += bit[pos + (1 << i)];  
                pos += (1 << i);  
            }  
        }  
        return pos + 1;  
    }  
};



```

### Range update 
```cpp
#include<bits/stdc++.h>
using namespace std;

const int N = 3e5 + 9;

struct BIT {
  long long M[N], A[N];
  BIT() {
    memset(M, 0, sizeof M);
    memset(A, 0, sizeof A);
  }
  void update(int i, long long mul, long long add) {
    while (i < N) {
      M[i] += mul;
      A[i] += add;
      i |= (i + 1);
    }
  }
  void upd(int l, int r, long long x) {
    update(l, x, -x * (l - 1));
    update(r, -x, x * r);
  }
  long long query(int i) {
    long long mul = 0, add = 0;
    int st = i;
    while (i >= 0) {
      mul += M[i];
      add += A[i];
      i = (i & (i + 1)) - 1;
    }
    return (mul * st + add);
  }
  long long query(int l, int r) {
    return query(r) - query(l - 1);
  }
} t;

int32_t main() {

  return 0;
}
```