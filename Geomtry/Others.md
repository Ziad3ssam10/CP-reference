```cpp
// find a circle of radius r that contains as many points as possible  
// O(n^2 log n)  
// Small EPS (1e-18) !  
pair<int, pt> maximum_circle_cover(vector<pt> p, ld r)  
{  
  int n = p.size();  
  int ans = 0;  
  int id = 0;  
  ld th = 0;  
  for (int i = 0; i < n; ++i)  
  {  
    // maximum circle cover when the circle goes through this point  
    vector<pair<ld, int>> events = {{-pi, +1}, {pi, -1}};  
    for (int j = 0; j < n; ++j)  
    {  
      if (j == i)  
        continue;  
      ld d = abs(p[i] - p[j]);  
      if (d > r * 2)  
        continue;  
      ld dir = arg(p[j] - p[i]);  
      ld ang = acos(d / 2 / r);  
      ld st = dir - ang, ed = dir + ang;  
      if (st > pi)  
        st -= pi * 2;  
      if (st <= -pi)  
        st += pi * 2;  
      if (ed > pi)  
        ed -= pi * 2;  
      if (ed <= -pi)  
        ed += pi * 2;  
      events.push_back({st - EPS, +1}); // take care of precisions!  
      events.push_back({ed, -1});  
      if (st > ed)  
      {  
        events.push_back({-pi, +1});  
        events.push_back({+pi, -1});  
      }  
    }  
    sort(events.begin(), events.end());  
    int cnt = 0;  
    for (auto &&e : events)  
    {  
      cnt += e.second;  
      if (cnt > ans)  
      {  
        ans = cnt;  
        id = i;  
        th = e.first;  
      }  
    }  
  }  
  pt w = pt(p[id].X + r * cos(th), p[id].Y + r * sin(th));  
  return {ans, w}; // points covered and center of the circle  
}  
  
// Finds the largest possible radius of a circle that can fit entirely inside a convex polygon using binary search
double maximum_inscribed_circle(vector<pt> p)  
{  
  int n = p.size();  
  if (n <= 2)  
    return 0;  
  double l = 0, r = 20000;  
  while (r - l > EPS)  
  {  
    double mid = (l + r) * 0.5;  
    vector<Halfplane> h;  
    const int L = 1e9;  
    h.push_back(Halfplane(pt(-L, -L), pt(L, -L)));  
    h.push_back(Halfplane(pt(L, -L), pt(L, L)));  
    h.push_back(Halfplane(pt(L, L), pt(-L, L)));  
    h.push_back(Halfplane(pt(-L, L), pt(-L, -L)));  
    for (int i = 0; i < n; i++)  
    {  
      pt z = perp(p[(i + 1) % n] - p[i]);  
      z = normalize(z);  
      z *= mid;  
      pt y = p[i] + z, q = p[(i + 1) % n] + z;  
      h.push_back(Halfplane(p[i] + z, p[(i + 1) % n] + z));  
    }  
    vector<pt> nw = hp_intersect(h);  
    if (!nw.empty())  
      l = mid;  
    else  
      r = mid;  
  }  
  return l;  
}  
  // Calculates the incircle (largest inscribed circle) of a triangle defined by points A, B, and C
Circle inCircle(pt A, pt B, pt C) // triangle ABC  
{  
  T a = abs(B - C);  
  T b = abs(C - A);  
  T c = abs(A - B);  
  pt I = (a * A + b * B + c * C) / (a + b + c); // incenter  
  
  T s = (a + b + c) / 2;  
  T area = sqrt(s * (s - a) * (s - b) * (s - c));  
  T r = area / s; // inradius  
  
  return Circle(I, r); // center and radius  
}
```

```cpp
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
    // if ab seg then t1 < -eps || t1 > 1 + eps    // if ab line then no checking.. same for cd but using t2    if (code & (1 << 3) and gt(t1, 1)) // seg for 1    //  return false;    if (code & (1 << 2) and lt(t1, 0))  
        return false;  
    if (code & (1 << 1) and gt(t2, 1))  
        return false;  
    if (code & (1) && lt(t2, 0))  
        return false;  
    return true;  
}
```