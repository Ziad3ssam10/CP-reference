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
  
  
### Key Identity  
  
$$  
\sum_{d\mid n}\mu(d)=  
\begin{cases}  
1 & n=1,\\  
0 & n>1.  
\end{cases}  
$$  
  
$$  
\mu * 1 = \varepsilon  
$$  
  
  
### Möbius Inversion  
  
$$  
f(n)=\sum_{d\mid n} g(d)  
$$  
  
$$  
\Longrightarrow  
$$  
  
$$  
g(n)=\sum_{d\mid n}\mu(d)\,f\!\left(\frac{n}{d}\right)  
=\sum_{d\mid n}\mu\!\left(\frac{n}{d}\right)f(d)  
$$  
  
  
### Euler Totient Application  
  
$$  
\sum_{d\mid n}\varphi(d)=n  
$$  
  
$$  
\varphi(n)=\sum_{d\mid n}\mu(d)\frac{n}{d}  
$$  
  
  
### Cyclotomic Formula  
  
$$  
x^n-1=\prod_{d\mid n}\Phi_d(x)  
$$  
  
$$  
\Phi_n(x)=\prod_{d\mid n}(x^d-1)^{\mu(n/d)}  
$$  
  
  
### Dirichlet Series  
  
$$  
\sum_{n=1}^{\infty}\frac{\mu(n)}{n^s}  
=\frac{1}{\zeta(s)},  
\quad \Re(s)>1  
$$  
  
  
### Useful Identities  
  
$$  
\sum_{d\mid n}\mu^2(d)=2^{\omega(n)}  
$$  
  
$$  
\sum_{d\mid n}\mu^2(d)\varphi(d)  
=\frac{n}{\varphi(n)}  
$$  
  
  
### Average Order  
  
$$  
\lim_{x\to\infty}  
\frac{1}{x}\sum_{n\le x}\mu(n)=0  
$$

### codes 
```cpp
mobius[1] = -1;
for (int i = 1; i < VALMAX; i++) {
	if (mobius[i]) {
		mobius[i] = -mobius[i];
		for (int j = 2 * i; j < VALMAX; j += i) { mobius[j] += mobius[i]; }
	}
}
```