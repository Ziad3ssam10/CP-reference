## **Diophantine Equations**

A **Diophantine equation** is an equation where solutions are restricted to integers. They either have no solutions or infinitely many.

- **Linear Diophantine Equation**: Takes the form _ax_+_by_=_c_.
    
    ax+by=c
    
    - **Existence of Solutions**: A solution exists if and only if _c_ is divisible by gcd(_a_,_b_).
        
    - **Solving**: If a solution exists, one particular solution (_x_0,_y_0) can be found using the extended Euclidean algorithm (specifically for _ax_+_by_=gcd(_a_,_b_) and then scaling).
        
    - **All Solutions**: Once a particular solution (_x_0,_y_0) for _ax_+_by_=_c_ is found, all integer solutions can be expressed as: _x_=_x_0−gcd(_a_,_b_)_br_ _y_=_y_0+gcd(_a_,_b_)_ar_ where _r_ is any integer.
        

# **Congruence**

Two integers a_a_ and b_b_ are **congruent modulo n**, written as a≡b(modn)_a_≡_b_(mod_n_), if they have the same remainder when divided by n_n_. This also means that (a−b)(_a_−_b_) is a multiple of n_n_.

- **Properties**:
    - If _ax_≡_ay_(mod_n_) and gcd(_a_,_n_)=_d_, then _x_≡_y_(mod_dn_).
    - For large powers (e.g., _aE_(mod_n_)), the exponent _E_ can often be reduced using modular arithmetic properties like Fermat's Little Theorem (_ap_−1≡1(mod_p_) for prime _p_).

## **Linear Modular Equation**

A **linear modular equation** is of the form ax≡b(modm)_ax_≡_b_(mod_m_).

- **Relationship to Diophantine Equations**: This equation is equivalent to a linear Diophantine equation: _ax_−_b_=_my_, which can be rewritten as _ax_−_my_=_b_.
- **Solutions**: Similar to linear Diophantine equations, solutions exist if _b_ is divisible by gcd(_a_,_m_).


```cpp
ll extended_euclid(ll a, ll b, ll &x, ll &y) {
  ll xx = y = 0;
  ll yy = x = 1;
  while (b) {
    ll q = a / b;
    ll t = b; b = a % b; a = t;
    t = xx; xx = x - q * xx; x = t;
    t = yy; yy = y - q * yy; y = t;
  }
  return a;
}
// a*x+b*y=c. returns valid x and y if possible.
// all solutions are of the form (x0 + k * b / g, y0 - k * b / g)
bool find_any_solution (ll a, ll b, ll c, ll &x0, ll &y0, ll &g) {
  if (a == 0 and b == 0) {
    if (c) return false;
    x0 = y0 = g = 0; 
    return true;
  }
  g = extended_euclid (abs(a), abs(b), x0, y0);
  if (c % g != 0) return false;
  x0 *= c / g;
  y0 *= c / g;
  if (a < 0) x0 *= -1;
  if (b < 0) y0 *= -1;
  return true;
}
void shift_solution(ll &x, ll &y, ll a, ll b, ll cnt) {
  x += cnt * b; // x = x + (b/g)
  y -= cnt * a;  // y = y + (a/g)
}
// returns the number of solutions where x is in the range[minx, maxx] and y is in the range[miny, maxy]
ll find_all_solutions(ll a, ll b, ll c, ll minx, ll maxx, ll miny,ll maxy) {
  ll x, y, g;
  if (find_any_solution(a, b, c, x, y, g) == 0) return 0;
  if (a == 0 and b == 0) {
    assert(c == 0);
    return 1LL * (maxx - minx + 1) * (maxy - miny + 1);
  }
  if (a == 0) {
    return (maxx - minx + 1) * (miny <= c / b and c / b <= maxy);
  }  
  if (b == 0) {
    return (maxy - miny + 1) * (minx <= c / a and c / a <= maxx);
  }
  a /= g, b /= g;
  ll sign_a = a > 0 ? +1 : -1;
  ll sign_b = b > 0 ? +1 : -1;
  shift_solution(x, y, a, b, (minx - x) / b);
  if (x < minx) shift_solution(x, y, a, b, sign_b);
  if (x > maxx) return 0;
  ll lx1 = x;
  shift_solution(x, y, a, b, (maxx - x) / b);
  if (x > maxx) shift_solution (x, y, a, b, -sign_b);
  ll rx1 = x;
  shift_solution(x, y, a, b, -(miny - y) / a);
  if (y < miny) shift_solution (x, y, a, b, -sign_a);
  if (y > maxy) return 0;
  ll lx2 = x;
  shift_solution(x, y, a, b, -(maxy - y) / a);
  if (y > maxy) shift_solution(x, y, a, b, sign_a);
  ll rx2 = x;
  if (lx2 > rx2) swap (lx2, rx2);
  ll lx = max(lx1, lx2);
  ll rx = min(rx1, rx2);
  if (lx > rx) return 0;
  return (rx - lx) / abs(b) + 1;
}
```


### LDE with N variables 
```cpp
ll extended_euclid(ll a, ll b, ll &x, ll &y) {
  ll xx = y = 0;
  ll yy = x = 1;
  while (b) {
    ll q = a / b;
    ll t = b; b = a % b; a = t;
    t = xx; xx = x - q * xx; x = t;
    t = yy; yy = y - q * yy; y = t;
  }
  return a;
}
// a * x + b * y = c. returns valid x and y if possible.
bool find_any_solution (ll a, ll b, ll c, ll &x0, ll &y0, ll &g) {
  if (a == 0 and b == 0) {
    if (c) return false;
    x0 = y0 = g = 0; 
    return true;
  }
  g = extended_euclid (abs(a), abs(b), x0, y0);
  if (c % g != 0) return false;
  x0 *= c / g;
  y0 *= c / g;
  if (a < 0) x0 *= -1;
  if (b < 0) y0 *= -1;
  return true;
}

// sum(a[i] * x[i]) = c, returns any valid solution. returns empty vector in case of failure
// x[i] can be any integer
// Complexity: O(n log(MAX))
// Optimization: You can reduce the number of variables to O(log (MAX)) instead of n
// by only considering those values which reduces suffix gcds
vector<ll> find_any_solution(vector<ll> a, ll c) {
  int n = a.size();
  vector<ll> x;
  bool all_zero = true;
  for (int i = 0; i < n; i++) {
    all_zero &= a[i] == 0;
  }
  if (all_zero) {
    if (c) return {};
    x.assign(n, 0);
    return x;
  }
  ll g = 0;
  for (int i = 0; i < n; i++) {
    g = __gcd(g, a[i]);
  }
  if (c % g != 0) return {};
  if (n == 1) {
    return {c / a[0]};
  }
  vector<ll> suf_gcd(n);
  suf_gcd[n - 1] = a[n - 1];
  for (int i = n - 2; i >= 0; i--) {
    suf_gcd[i] = __gcd(suf_gcd[i + 1], a[i]);
  }
  ll cur = c;
  for (int i = 0; i + 1 < n; i++) {
    ll x0, y0, g;
    // solve for a[i] * x + suf_gcd[i + 1] * (y / suf_gcd[i + 1]) = cur
    bool ok = find_any_solution(a[i], suf_gcd[i + 1], cur, x0, y0, g);
    assert(ok);
    {
      // trying to minimize x0 in case x0 becomes big
      // it is needed for this problem, not needed in general
      ll shift = abs(suf_gcd[i + 1] / g);
      x0 = (x0 % shift + shift) % shift;
    }
    x.push_back(x0);

    // now solve for the next suffix
    cur -= a[i] * x0;
  }
  x.push_back(a[n - 1] == 0 ? 0 : cur / a[n - 1]);
  return x;
}
```

### CRT 
```cpp
int egcd(int a, int b, int &x, int &y) {
    if (b == 0) {
        x = 1, y = 0;
        return a;
    }
    int x1, y1;
    int g = egcd(b, a % b, x1, y1);
    x = y1;
    y = x1 - (a / b) * y1;
    return g;
}

// Solves system: x ≡ a1 mod m1 and x ≡ a2 mod m2
// Returns {x, lcm(m1, m2)} if solution exists, else {-1, -1}
pair<int, int> merge(int a1, int m1, int a2, int m2) {
    int x, y;
    int g = egcd(m1, m2, x, y);
    if ((a2 - a1) % g != 0)return {-1, -1};
    int lcm = m1 / g * m2;
    int diff = ((a2 - a1) / g) % (m2 / g);
    int mult = x % (m2 / g);
    int ret = (a1 + m1 * ((diff * mult) % (m2 / g))) % lcm;
    if (ret < 0) ret += lcm;
    return {ret, lcm};
}

```

### inverse any mod 
```cpp
ll inverse(ll a, ll m) {
    ll x, y;
    ll g = extended_euclid(a, m, x, y);
    if (g != 1) {
        return -1;
    }
    return (x % m + m) % m;
}
```