$$\sigma(n) = \prod_{i=1}^{k}(1 + p_{i} + ... + p_{i}^{\alpha_i}) = \prod_{i=1}^{k}\frac{p_{i}^{\alpha_i + 1} - 1}{p_i - 1}$$

$$\sum_{i=1}^{n} i^2 = \frac{n(n+1)(2n+1)}{6}$$

$$\sum_{i=1}^{n} i^3 = (\frac{n(n+1)}{2})^2$$

$$\sum_{i=1}^{n} i^4 = \frac{n(n+1)(2n+1)(3n^2+3n-1)}{30}$$

$$\sum_{i=1}^{n} i^5 = \frac{(n(n+1))^2(2n^2+2n-1)}{12}$$

$$\sum_{i=0}^{n} x^i = \frac{x^{n+1}-1}{x-1} \text{ para } x \neq 1$$


**1. Number of Divisors of $N$**
For prime factorization $N = p_1^{a_1} \cdot p_2^{a_2} \cdots p_k^{a_k}$, the total number of divisors $d(N)$ is:
$$d(N) = \prod_{i=1}^k (a_i + 1)$$

---

**2. Count of Pairs $(X, Y)$ where $\text{lcm}(X, Y) \le N$**
The total count of valid pairs is the sum of the divisors of $k^2$ for all $k$ up to $N$:
$$\text{Total Pairs} = \sum_{k=1}^{N} d(k^2)$$

---

**3. Count of Pairs $(X, Y)$ where $\text{gcd}(X, Y) \le N$**
* **Unbounded $X, Y$:** $\infty$
* **Bounded ($1 \le X, Y \le N$):** $N^2$
* **Coprime pairs ($\text{gcd}(X, Y) = 1$ for $1 \le X, Y \le N$):** $\approx \frac{3}{\pi^2} N^2$

2 -> last digit is even

3 -> sum of its digits is divisible by 3

4 -> last two digits are divisible by 4

5 -> last digit is either 0 or 5

6 -> apply divisibility rules of both 2 and 3

7 -> a little bit complicated

8 -> if the last three numbers are divisible by 8

9 -> sum of digits is divisible by 9

10 -> last digit is 0

11 -> if the absolute difference between the sum of numbers in odd places and the sum of numbers

in even places is divisible by 11

12 -> number is divisible by both 3 and 4

13 -> a little bit complicated

25 -> last two digits form a number divisible by 25

```cpp
// Every even integer greater than 2 can be sum of 2 prime numbers

if (x > 2 and (x % 2 == 0 or isPrime(x - 2)))

    cout << "YES\n";

else cout << "NO\n";
```