### Pascal
```cpp
// C[n][r] = nCr  
void buildPascal() {  
    for (int i = 0; i <= N; i++) {  
        for (int j = 0; j <= i; j++) {  
            if (j == 0)  
                C[i][j] = 1;  
            else  
                C[i][j] = C[i - 1][j] + C[i - 1][j - 1];  
        }  
    }  
}
```

# Stars and Bars

### Number of non-negative integer solutions to

$$x1+x2+⋯+xk=nx_1 + x_2 + \dots + x_k = nx1​+x2​+⋯+xk​=n$$

$$\binom{n+k-1}{k-1}$$


$$\sum_{k=r}^{n} \binom{k}{r} = \binom{n+1}{r+1}$$



$$\sum_{k=0}^{n} k \binom{n}{k} = n \cdot 2^{\,n-1}$$


$$\sum_{k=0}^{n} \binom{n}{k}^2 = \binom{2n}{n}$$


$$\sum_{k=0}^{n} (-1)^k \binom{n}{k} = 0 \quad \text{for } n \ge 1$$


$$\sum_{k=0}^{n} \binom{n}{k} = 2^n$$





$$\sum_{k=0}^{n} \binom{n}{k} 
=
\sum_{\substack{k=0 \\ k \text{ even}}}^{n} \binom{n}{k}
+
\sum_{\substack{k=0 \\ k \text{ odd}}}^{n} \binom{n}{k}
= 2^{n-1}
\quad \text{for } n \ge 1$$





$$\sum_{k=1}^{n} k^2 = \frac{n(n+1)(2n+1)}{6}$$


$$\sum_{k=1}^{n} k^3 = \left( \frac{n(n+1)}{2} \right)^2$$




