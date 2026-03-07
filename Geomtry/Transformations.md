```cpp
// Shifts point p by the displacement vector v
pt translate(pt v, pt p) { return p + v; }

// Scales point p relative to a center c by a given factor
pt scale(pt c, T factor, pt p) { return c + (p - c) * factor; }

// Rotates point p around center c by an angle a (in radians)
pt rot(pt p, pt c, ld a) { return c + pt(cos(a), sin(a)) * (p - c); }

// Returns a unit vector in the same direction as vector a
pt normalize(pt a) { return a / abs(a); }

// Performs a similarity transformation mapping segment pq to segment fp-fq, applied to point r
pt linearTransfo(pt p, pt q, pt r, pt fp, pt fq) { return fp + (r - p) * (fq - fp) / (q - p); }
```