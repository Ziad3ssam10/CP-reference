```cpp
struct Line {  
    pt v;  
    T c;  
  
    Line(pt v, T c) : v(v), c(c) {  
    }  
  
    // from equation ax+by = c  
    Line(T a, T b, T _c) {  
        v = {b, -a};  
        c = _c;  
    }  
  
    // line from two points  
    Line(pt p, pt q) {  
        v = q - p;  
        c = cross(v, p);  
    }  
  
    T side(pt p) { return cross(v, p) - c; } // test point location with line 0 if on the line   
    ld dist(pt p) { return abs(side(p)) / abs(v); }  
    double sqDist(pt p) { return side(p) * side(p) / (T) sq(v); }  
    //new line perpendicular to this one passing through p
    Line perpThrought(pt p) { return {p, p + perp(v)}; }  
  
    bool cmpProj(pt p, pt q) {  
        return dot(v, p) < dot(v, q);  
    }  
  // Moves the line by displacement vector t
    Line translate(pt t) { return {v, c + cross(v, t)}; }  
    Line shiftLeft(T dist) { return {v, c + dist * abs(v)}; } // shift right by negative dist  
    // Returns the point on the line closest to p (orthogonal projection)
    pt proj(pt p) { return p - perp(v) * side(p) / sq(v); }  
    pt refl(pt p) { return p - perp(v) * (T) 2.0 * side(p) / sq(v); }  
};  
  
bool inter(Line l1, Line l2, pt &out) // out is the intersection point  
{  
    T d = cross(l1.v, l2.v);  
    if (fabs(d) < EPS)  
        return false;  
    out = (l2.v * l1.c - l1.v * l2.c) / d; // requires floating-point coordinates  
    return true;  
}  
  // Returns the angle bisector between two lines (interior or exterior based on bool)
Line bisector(Line l1, Line l2, bool interior) {  
    assert(cross(l1.v, l2.v) != 0); // l1 and l2 cannot be parallel!  
    T sign = interior ? 1 : -1;  
    return {l2.v / abs(l2.v) + l1.v / abs(l1.v) * sign, l2.c / abs(l2.v) + l1.c / abs(l1.v) * sign};  
}  

bool intersectll(line a, line b, point &intersect)  
{  
    ld D = cross(a.n, b.n);  
    if (fabsl(D) < eps) return 0;  
    point p1 = a.n * (a.c / dot(a.n, a.n));  
    point d1 = point(-a.n.Y, a.n.X);  
    point p2 = b.n * (b.c / dot(b.n, b.n));  
    point d2 = point(-b.n.Y, b.n.X);  
    ld t = cross(p2 - p1, d2) / cross(d1, d2);  
    intersect = p1 + d1 * t;  
    return 1;  
}
  
Line normalizeLine(pt a, pt b) {  
    int A = b.Y - a.Y;  
    int B = b.X - a.X;  
    int g = gcd(A, B);  
    A /= g;  
    B /= g;  
    if (A < 0 or A == 0 and B < 0)  
        A *= -1, B *= -1;  
    int C = cross(pt(B, A), a);  
    return Line(A, B, C);  
}


// hadnle segment aa  
// 00  01   11  
//line-ray-segment  
// line line 0000 0  
// line ray  0001 1  
// line seg  0011 3  
// ray line  0100 4  
// ray ray   0101 5  
// ray seg   0111 7  
// seg line  1100 12  
// seg ray   1101 13  
// seg seg   1111 15  
bool intersectll(point a, point b, point c, point d, point &intersect, int code) {  
    ld d1 = cross(a - b, d - c);  
    if (lt(abs(d1), 0))  
        return false;  
    ld t1 = cross(a - c, d - c) / d1;  
    ld t2 = cross(a - b, a - c) / d1;  
    intersect = a + (b - a) * t1;  
    // if ab ray then t1 < -eps  
    // if ab seg then t1 < -eps || t1 > 1 + eps    // if ab line then no checking.. same for cd but using t2      
if (code & (1 << 3) and gt(t1, 1)) // seg for 1      
return false;  
    if (code & (1 << 2) and lt(t1, 0))  
        return false;  
    if (code & (1 << 1) and gt(t2, 1))  
        return false;  
    if (code & (1) && lt(t2, 0))  
        return false;  
    return true;  
}
```