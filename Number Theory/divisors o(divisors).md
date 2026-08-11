### precompute primes till SQRT
```cpp
void gen(int i, int c) {  
    if (i == factors.size()) {  
        divs.push_back(c);  
        return;  
    }  
    int p = 1;  
    for (int j = 0; j <= factors[i].second; ++j) {  
        gen(i + 1, c * p);  
        p *= factors[i].first;  
    }  
}  
  
void getdivs(ll n) {  
    factors.clear();  
    divs.clear();  
    for (int p: primes) {  
        if (1LL * p * p > n) break;  
        if (n % p == 0) {  
            int c = 0;  
            while (n % p == 0) {  
                c++;  
                n /= p;  
            }  
            factors.push_back({p, c});  
        }  
    }  
    if (n > 1) factors.push_back({n, 1});  
    gen(0, 1);  
    sort(divs.begin(), divs.end());  
}
```