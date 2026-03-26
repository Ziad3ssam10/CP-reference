### Manhattan Trick Reference

**Core Transformation:**

Map every point $(x, y)$ to $(u, v)$:

- $u = x + y$
    
- $v = x - y$
    

**Distance Conversion:**

The Manhattan distance between $(x_1, y_1)$ and $(x_2, y_2)$ becomes the Chebyshev (maximum) distance between $(u_1, v_1)$ and $(u_2, v_2)$:

$d = \max(|u_1 - u_2|, |v_1 - v_2|)$

**Max Distance in $O(N)$:**

Instead of checking all pairs, the maximum Manhattan distance in a set of points is simply the largest 1D spread in either the $u$ or $v$ dimension:

$\max(\max(u) - \min(u), \max(v) - \min(v))$


### Generalization
for k dimension space try all possible for + - and the same as 2d space 

