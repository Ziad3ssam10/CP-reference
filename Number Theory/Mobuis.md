### Möbius Function  
  
$$  
\mu(n)=  
\begin{cases}  
1 & \text{if } n=1,\\  
0 & \text{if } p^2 \mid n \text{ for some prime } p,\\  
(-1)^k & \text{if } n \text{ is the product of } k \text{ distinct primes.}  
\end{cases}  
$$  
  
$$  
\mu(n)\in\{-1,0,1\}, \quad \mu(p)=-1 \text{ for prime } p.  
$$  
  
  
  
### Möbius Inversion  
  ```cpp
  // Given X

// How many i s.t gcd(X, i) = 1

// 1 <= i <= n

// sum over d (mob[d] * (n / d))

// d is a divisor of X

  

// Count pairs gcd(ai, aj) = 1

// frq[i] = frq of all multiples of i

  

/*

for (int i = 1; i < N; ++i)

        ans += mob[i] * (1LL * frq[i] * (frq[i] - 1)) / 2;

*/  

// If you want subset of k

  // mob[i] * (frq[i]Ck)

  

//   f(n) = sum i, j gcd(i, j)

// can do with it the same coprime counting -> tot C k

  

// When

// f(n) = sum i, j Lcm(a_i, a_j)

  

// replace 1+n/l * n/l with S(L)

// S(L) = sum of all elements divisible by L
  ```  
  
  

### codes 
```cpp
mobius[1] = -1;
for (int i = 1; i < VALMAX; i++) {
	if (mobius[i]) {
		mobius[i] = -mobius[i];
		for (int j = 2 * i; j < VALMAX; j += i) { mobius[j] += mobius[i]; }
	}
}


const int N = 1e7 + 5;    
int mob[N]{0};    
int spf[N], pr[N], sz;    
void mobius() {    
    mob[1] = 1;    
    for (int i = 2; i < N; ++i) {    
        if (!spf[i]) spf[i] = i, pr[sz++] = i, mob[i] = -1;    
        for (int j = 0; pr[j] * i < N; ++j) {    
            spf[i * pr[j]] = pr[j];    
            if (spf[i] == pr[j])    
                break;    
            mob[i * pr[j]] = -mob[i];    
        }    
    }    
}
```