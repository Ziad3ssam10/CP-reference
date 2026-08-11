### Conventions & Core Functions
* `arg(complex<double>)`: Angle between the x-axis and vector ($\operatorname{atan2}(y, x)$).
* Radians to degrees: $\text{angle} \times \frac{180}{\pi}$
* Degrees to radians: $\text{angle} \times \frac{\pi}{180}$
* Vector from magnitude and $\theta$: $x = r \cos(\theta)$, $y = r \sin(\theta)$
* Multiply vectors: Magnitude = $r_1 \cdot r_2$, Angle = $\theta_1 + \theta_2$
* Polygon orientation: Remove `abs` from `polygonArea`; positive area $\implies$ clockwise.

---

### Triangles & Trigonometry
* **Triangle Area:** $A = \frac{1}{2} a b \sin(\theta)$
* **Law of Sines:** 
$$\frac{a}{\sin(A)} = \frac{b}{\sin(B)} = \frac{c}{\sin(C)} = 2R$$
* **Law of Cosines:** 
$$c^2 = a^2 + b^2 - 2ab \cos(C)$$
*(Note: To solve for the angle, this rearranges to $\cos(C) = \frac{a^2 + b^2 - c^2}{2ab}$)*
* **Inradius ($r$):** 
$$r = \frac{2 \cdot \text{Area}}{a + b + c}$$
* **Circumradius ($R$):** 
$$R = \frac{a b c}{4 \cdot \text{Area}}$$
* **Distance between Circumcenter and Incenter (Euler's theorem):** 
$$OI^2 = R(R - 2r)$$

---

### Advanced Polygon Formulas
* **Pick’s Theorem (Lattice Polygons):** 
$$A = I + \frac{B}{2} - 1$$
*(Where $I$ = interior points, $B$ = boundary points)*
* **Shoelace Formula (Polygon Area):** 
$$A = \frac{1}{2} \left| \sum_{i=1}^{n-1} (x_i y_{i+1} - x_{i+1} y_i) \right|$$

---

### 2D Shapes (Area & Perimeter)
* **Rectangle:** $A = w \cdot h$, $P = 2(w + h)$
* **Square:** $A = a^2$, $P = 4a$
* **Triangle:** $A = \frac{1}{2} b h$, $P = a + b + c$
* **Heron’s Formula:** 
$$A = \sqrt{s(s-a)(s-b)(s-c)} \quad \text{where} \quad s = \frac{a+b+c}{2}$$
* **Parallelogram:** $A = b \cdot h$, $P = 2(a + b)$
* **Trapezoid:** $A = \frac{b_1 + b_2}{2} \cdot h$, $P = a + b_1 + b_2 + c$
* **Circle:** $A = \pi r^2$, $C = 2\pi r$
* **Sector** *(angle $\theta$ in radians)*: $A = \frac{1}{2} r^2 \theta$, $P = r\theta + 2r$
* **Regular $n$-gon** *(side $a$)*: 
$$A = \frac{n}{4} a^2 \cot\left(\frac{\pi}{n}\right), \quad P = n \cdot a$$

---

### 3D Solids (Volume & Surface Area)
* **Cuboid:** 
  * $V = a b c$
  * $S = 2(ab + bc + ca)$
* **Sphere:** 
  * $V = \frac{4}{3} \pi r^3$
  * $S = 4\pi r^2$
* **Cylinder:** 
  * $V = \pi r^2 h$
  * $S = 2\pi r(h + r)$
* **Cone:** 
  * $V = \frac{1}{3} \pi r^2 h$
  * $S = \pi r(r + \sqrt{h^2 + r^2})$
* **Pyramid:** 
  * $V = \frac{1}{3} B h$
  * $S = B + \frac{1}{2} P l \quad$ *(where $B$ = base area, $P$ = base perimeter, $l$ = slant height)*