```cpp
struct Circle  
{  
  pt o;  
  T r;  
  
  Circle() {}  
  Circle(pt o, T r) : o(o), r(r) {}  
  
  Circle(pt a, pt b, pt c)  
  {  
    b -= a, c -= a;  
    assert(cross(b, c) != 0);  
    pt center = a + perp(b * sq(c) - c * sq(b)) / cross(b, c) / (T)2;  
    T radius = abs(center - a);  
    r = radius;  
    o = center;  
  }  
  
  // Line intersection: returns number of intersection points, stores them in 'out'  
  int circleLine(Line l, pair<pt, pt> &out) const  
  {  
    T h2 = r * r - l.sqDist(o);  
    if (h2 >= 0)  
    {  
      pt p = l.proj(o);  
      pt h = l.v * (T)(sqrt(h2) / abs(l.v));  
      out = {p - h, p + h};  
    }  
    return 1 + sgn(h2);  
  }  
  
  // Circle-circle intersection: returns number of intersection points, stores them in 'out'  
  int circleCircle(pt o1, T r1, pt o2, T r2, pair<pt, pt> &out)  
  {  
    pt d = o2 - o1;  
    T d2 = sq(d);  
    if (d2 == 0)  
    {  
      assert(r1 != r2);  
      return 0;  
    } // concentric circles  
    T pd = (d2 + r1 * r1 - r2 * r2) / 2; // = |O_1P| * d  
    T h2 = r1 * r1 - pd * pd / d2;       // = hˆ2  
    if (h2 >= 0)  
    {  
      pt p = o1 + d * pd / d2, h = perp(d) * (T)sqrt(h2 / d2);  
      out = {p - h, p + h};  
    }  
    return 1 + sgn(h2);  
  }  
  
  // Tangents from this to another circle: returns number of tangent lines, fills 'out'  
  int tangents(const Circle &c2, bool inner, vector<pair<pt, pt>> &out) const  
  {  
    T r2 = inner ? -c2.r : c2.r;  
    pt d = c2.o - o;  
    T dr = r - r2, d2 = sq(d), h2 = d2 - dr * dr;  
    if (d2 == 0 || h2 < 0)  
    {  
      assert(h2 != 0);  
      return 0;  
    }  
    for (T sign : {-1, 1})  
    {  
      pt v = (d * dr + perp(d) * (T)sqrt(h2) * sign) / d2;  
      out.push_back({o + v * r, c2.o + v * r2});  
    }  
    return 1 + (h2 > 0);  
  }  
  
  // Area of intersection with another circle  
  ld intersection_area(const Circle &c) const  
  {  
    ld dx = o.X - c.o.X;  
    ld dy = o.Y - c.o.Y;  
    ld d = sqrtl(dx * dx + dy * dy);  
  
    if (d >= r + c.r)  
      return 0.0L;  
    if (d + c.r <= r)  
      return pi * c.r * c.r;  
    if (d + r <= c.r)  
      return pi * r * r;  
  
    // angle and segment area for c  
    ld ct = (c.r * c.r + d * d - r * r) / (2.0L * c.r * d);  
    ld theta = acosl(ct);  
    ld area1 = c.r * c.r * (theta - sinl(theta) * cosl(theta));  
  
    // angle and segment area for this circle  
    ct = (r * r + d * d - c.r * c.r) / (2.0L * r * d);  
    theta = acosl(ct);  
    ld area2 = r * r * (theta - sinl(theta) * cosl(theta));  
  
    return area1 + area2;  
  }  
  bool operator==(const Circle &c) const { return c.o == o and sgn(c.r - r) == 0 and sgn(r) > 0; }  
};
```


```cpp
struct Circle {  
    point center;  
    ld r;  
   
    Circle() {  
    }  
   
    Circle(point _o,ld _r) : center(_o), r(_r) {  
    }  
   
    Circle(point a,point b,point c) {  
        point m1 = (b + a) * 0.5l, v1 = b - a;  
        point pv1 = prep(v1);  
        point m2 = (b + c) * 0.5l, v2 = b - c;  
        point pv2 = prep(v2);  
        point end1 = m1 + pv1, end2 = m2 + pv2, cen;  
        intersectll(m1, end1, m2, end2, cen, 15);  
        this->center = cen;  
        this->r = abs(a - center);  
    }  
   
    bool operator ==(Circle v) { return center == v.center && sign(r - v.r) == 0; }  
    double area() { return pi * r * r; }  
    double circumference() { return 2.0 * pi * r; }  
};  
   
// 0 -> outside , 1 -> on circumference , 2-> inside  
int Circlepointrel(Circle cir,point a) {  
    ld d = dist(cir.center - a);  
    if (sign(d - cir.r) < 0)return 2;  
    if (sign(d - cir.r) == 0)return 1;  
    return 0;  
}  
   
int Circlelinerel(Circle cir, line l) {  
    ld d = l.dist(cir.center);  
    if (sign(d - cir.r) < 0)return 2;  
    if (sign(d - cir.r) == 0)return 1;  
    return 0;  
}  
   
vector<point > Circlelineinter(Circle cir, line l) {  
    ld d = l.dist(cir.center);  
    if (gt(d, cir.r)) return {};  
    point p = l.proj(cir.center);  
    if (eq(d, cir.r)) return {p};  
    ld h = sqrt(cir.r * cir.r - d * d);  
    point dir = prep(l.n);  
    dir = normalize(dir) * h;  
    return {p + dir, p - dir};  
}  
   
// return:  
// 0 -> no intersect (separated)  
// 1 -> external tangent  
// 2 -> two intersection points  
// 3 -> internal tangent  
// 4 -> one inside another (disjoint)  
// 5 -> identical circles  
int cirCirRel(Circle A, Circle B) {  
    ld d = abs(A.center - B.center);  
    ld r1 = A.r, r2 = B.r;  
    if (same(A.center, B.center) and eq(r1, r2))return 5;  
    if (gt(d, r1 + r2))return 0;  
    if (lt(d, abs(r1 - r2)))return 4;  
    if (eq(d, r1 + r2))return 1;  
    if (eq(d, abs(r1 - r2)))return 3;  
    return 2;  
}  
   
vector<point > CircleCircleInter(Circle A, Circle B) {  
    point c1 = A.center, c2 = B.center;  
    ld r1 = A.r, r2 = B.r;  
    ld d = abs(c1 - c2);  
    if (same(c1, c2) and eq(r1, r2) && gt(r1, 0))return vector<point >(3, c1);  
    if (gt(d, r1 + r2)) return {};  
    if (lt(d, abs(r1 - r2))) return {};  
    ld ang1 = angvecx(c2 - c1);  
    ld ang2 = getAngle_A_abc(r2, r1, d);  
    if (isnan(ang2)) ang2 = 0;  
    point p1 = polar(r1, ang1 + ang2) + c1;  
    if (eq(dot(p1 - c1, p1 - c1), r1 * r1) == false or eq(dot(p1 - c2, p1 - c2), r2 * r2) == false)return {};  
    point p2 = polar(r1, ang1 - ang2) + c1;  
    vector<point > ans;  
    ans.push_back(p1);  
    if (!same(p1, p2)) ans.push_back(p2);  
   
    return ans;  
}  
   
Circle minimum_enclosing_circle(vector<point > p) {  
    static std::mt19937 rng((unsigned) chrono::steady_clock::now().time_since_epoch().count());  
    shuffle(p.begin(), p.end(), rng);  
    int n = (int) p.size();  
    Circle c(point(0, 0), 0);  
    if (n == 0) return c;  
    c = Circle(p[0], 0);  
    for (int i = 1; i < n; ++i) {  
        if (sign(abs(c.center - p[i]) - c.r) > 0) {  
            c = Circle(p[i], 0);  
            for (int j = 0; j < i; ++j) {  
                if (sign(abs(c.center - p[j]) - c.r) > 0) {  
                    point cen = (p[i] + p[j]) / 2.0L;  
                    c = Circle(cen, abs(p[i] - cen));  
                    for (int k = 0; k < j; ++k) {  
                        if (sign(abs(c.center - p[k]) - c.r) > 0) {  
                            c = Circle(p[i], p[j], p[k]);  
                        }  
                    }  
                }  
            }  
        }  
    }  
    return c;  
}
```