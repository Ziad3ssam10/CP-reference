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
```