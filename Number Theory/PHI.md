Euler's totient function, also known as **phi-function**  $\phi (n)$ , counts the number of integers between 1 and  $n$  inclusive, which are coprime to  $n$ . Two numbers are coprime if their greatest common divisor equals  $1$


- If  $p$  is a prime number, then  $\gcd(p, q) = 1$  for all  $1 \le q < p$ . Therefore we have:

 $$\phi (p) = p - 1.$$ 

- If  $p$  is a prime number and  $k \ge 1$ , then there are exactly  $p^k / p$  numbers between  $1$  and  $p^k$  that are divisible by  $p$ . Which gives us:

 $$\phi(p^k) = p^k - p^{k-1}.$$ 

- If  $a$  and  $b$  are relatively prime, then:
    
      $$\phi(a b) = \phi(a) \cdot \phi(b).$$


Codes 

### phi in sqrt
```cpp
int phi(int n) {
    int result = n;
    for (int i = 2; i * i <= n; i++) {
        if (n % i == 0) {
            while (n % i == 0)
                n /= i;
            result -= result / i;
        }
    }
    if (n > 1)
        result -= result / n;
    return result;
}
```

### Phi from 1 to n 
```cpp
void phi_1_to_n(int n) {
    vector<int> phi(n + 1);
    phi[0] = 0;
    phi[1] = 1;
    for (int i = 2; i <= n; i++)
        phi[i] = i - 1;

    for (int i = 2; i <= n; i++)
        for (int j = 2 * i; j <= n; j += i)
              phi[j] -= phi[i];
}

```

### Segmented phi
```cpp
const long long MAX_RANGE = 1e6 + 6;
vector<long long> primes;
long long phi[MAX_RANGE], rem[MAX_RANGE];

vector<int> linear_sieve(int n) { 
    vector<bool> composite(n + 1, 0);
    vector<int> prime;

    // 0 and 1 are not composite (nor prime)
    composite[0] = composite[1] = 1;

    for(int i = 2; i <= n; i++) {
        if(!composite[i]) prime.push_back(i);
        for(int j = 0; j < prime.size() && i * prime[j] <= n; j++) {
            composite[i * prime[j]] = true;
            if(i % prime[j] == 0) break;
        }
    }
    return prime;
}

// To get the value of phi(x) for L <= x <= R, use phi[x - L].
void segmented_phi(long long L, long long R) { 
    for(long long i = L; i <= R; i++) {
        rem[i - L] = i;
        phi[i - L] = i;
    }

    for(long long i : primes) {
        for(long long j = max(i * i, (L + i - 1) / i * i); j <= R; j += i) {
            phi[j - L] -= phi[j - L] / i;
            while(rem[j - L] % i == 0) rem[j - L] /= i;
        }
    }

    for(long long i = 0; i < R - L + 1; i++) {
        if(rem[i] > 1) phi[i] -= phi[i] / rem[i];
    }
}
```

```cpp
const int N = 1e6 + 5;  
int phi[N], g[N], lcm_sum[N];  
vector<int> divi[N];  
// phi[i] = number of integers j that are coprime with i (j <= i)  
// g[i] = sum of gcd(a, b) for all 1 ≤ a < b ≤ i  
// lcm_sum[i] = sum of LCM(j,i) for 1 ≤ j ≤ i  
void euler() {  
    for (int i = 1; i < N; ++i)  
        phi[i] = i;  
    for (int i = 2; i < N; ++i) {  
        if (phi[i] == i) {  
            for (int j = i; j < N; j += i) {  
                phi[j] -= phi[j] / i;  
            }  
        }  
    }  
    for (int i = 1; i < N; ++i) {  
        for (int j = i; j < N; j += i) {  
            divi[j].push_back(i);  
        }  
    }  
    for (int d = 1; d < N; ++d) {  
        for (int k = d << 1; k < N; k += d) {  
            g[k] += d * phi[k / d];  
        }  
    }  
    for (int i = 1; i < N; ++i)  
        g[i] += g[i - 1];  
    for (int n = 1; n < N; ++n) {  
        lcm_sum[n] = 0;  
        for (int &d: divi[n]) {  
            lcm_sum[n] += phi[d] * d;  
        }  
        lcm_sum[n] = n * (lcm_sum[n] + 1) / 2; // +1 to account for d = n  
    }  
}
```