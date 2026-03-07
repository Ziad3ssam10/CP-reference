```cpp
const int inf = 1e18;  
const ld EPS = 1e-9;  
const ld pi = acos(-1);  
typedef ld T;  
typedef complex<T> pt;  
#define X real()  
#define Y imag()  
  
```

```cpp
 // comparison
 bool lt(ld a, ld b) { return a < b - eps; }
 bool gt(ld a, ld b) { return lt(b, a); }
 bool lteq(ld a, ld b) { return !lt(b, a); }
 bool gteq(ld a, ld b) { return !lt(a, b); }
 bool eq(ld a, ld b) { return abs(a - b) <= eps; }
```
  
```cpp
bool cmp(const pt &a, const pt &b) { return make_pair(a.X, a.Y) < make_pair(b.X, b.Y); }  
T sq(pt p) { return p.X * p.X + p.Y * p.Y; }  
T dot(pt v, pt w) { return v.X * w.X + v.Y * w.Y; }  
T cross(pt v, pt w) { return v.X * w.Y - v.Y * w.X; }  
ld dist(pt a, pt b) { return sqrtl(sq(a - b)); }  
int sgn(T val) { return (val > EPS) - (val < -EPS); }  
bool isPerp(pt v, pt w) { return fabs(dot(v, w)) < EPS; }  
pt perp(pt p) { return {-p.Y, p.X}; }  
ld toDeg(ld ang) { return ang * 180.0 / pi; }  
ld toRad(ld ang) { return ang * pi / 180.0; }  
bool same(pt a, pt b) { return !sgn(a.X - b.X) and !sgn(a.Y - b.Y); }
```

### angles
```cpp
//positive for CCW, negative for CW
T orient(pt a, pt b, pt c) { return cross(b - a, c - a); }  
T clamp(T val, T low, T high) { return max(low, min(val, high)); }  
ld angle(pt v, pt w) { return acos(clamp(dot(v, w) / abs(v) / abs(w), (T) - 1.0, (T) 1.0)); }  
// x,y adjacent sides | z opposite side  
ld angle(T x, T y, T z) { return acos(clamp((x * x + y * y - z * z) / (2 * x * y), (T) - 1.0, (T) 1.0)); }  
  
T orientedAngle(pt a, pt b, pt c) {  
    ld ampli = angle(b - a, c - a);  
    if (orient(a, b, c) > 0)  
        return ampli;  
    else  
        return 2 * pi - ampli;  
}  
  
T angleTravelled(pt a, pt b, pt c) {  
    ld ampli = angle(b - a, c - a);  
    if (orient(a, b, c) > 0)  
        return ampli;  
    else  
        return -ampli;  
}  
  
// check p in between angle(bac) counter clockwise  
bool inAngle(pt a, pt b, pt c, pt p) {  
    T abp = orient(a, b, p), acp = orient(a, c, p), abc = orient(a, b, c);  
    if (abc < 0)  
        swap(abp, acp);  
    return (abp >= 0 && acp <= 0) ^ (abc < 0);  
}
```