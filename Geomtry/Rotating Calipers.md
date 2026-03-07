```cpp
// Computes max distance between any two points (diameter) of a convex polygon  
ld farthest_pair(vector<pt> &p)  
{  
  vector<pt> hull = convex_hull(p);  
  int m = hull.size();  
  int j = 1;  
  ld d = 0;  
  // rotating calipers  
  for (int i = 0; i < m; ++i)  
  {  
    while (norm(hull[i] - hull[(j + 1) % m]) > norm(hull[i] - hull[j]))  
      j = (j + 1) % m;  
    d = max((T)d, norm(hull[i] - hull[j]));  
  }  
  return sqrtl(d);  
}  
  
// Computes min width of a parallel strip that can enclose all points  
ld min_width(vector<pt> p)  
{  
  vector<pt> hull = convex_hull(p);  
  int m = hull.size();  
  if (m <= 2)  
    return 0;  
  ld ret = 1e18;  
  // rotating calipers  
  for (int i = 0, j = 1; i < m; ++i)  
  {  
    pt u = hull[i];  
    pt v = hull[(i + 1) % m];  
    while (areaTriangle(u, v, hull[(j + 1) % m]) > areaTriangle(u, v, hull[j]))  
      j = (j + 1) % m;  
    ld height = 2 * areaTriangle(u, v, hull[j]) / dist(u, v);  
    ret = min(ret, height);  
  }  
  return ret;  
}  
  
// area and perimeter of minimum enclosing rectangle  
pair<ld, ld> minimum_enclosing_rectangle(vector<pt> &p)  
{  
  int n = p.size();  
  if (n <= 2)  
  {  
    ld d = 0;  
    for (int i = 0; i < n; ++i)  
      d += dist(p[i], p[(i + 1) % n]);  
    return {0, d};  
  }  
  int mndot = 0;  
  double tmp = dot(p[1] - p[0], p[0]);  
  for (int i = 1; i < n; i++)  
  {  
    if (dot(p[1] - p[0], p[i]) <= tmp)  
    {  
      tmp = dot(p[1] - p[0], p[i]);  
      mndot = i;  
    }  
  }  
  ld perim = 1e18, area = 1e18;  
  int i = 0, j = 1, mxdot = 1;  
  while (i < n)  
  {  
    pt cur = p[(i + 1) % n] - p[i];  
    while (cross(cur, p[(j + 1) % n] - p[j]) >= 0)  
      j = (j + 1) % n;  
    while (dot(p[(mxdot + 1) % n], cur) >= dot(p[mxdot], cur))  
      mxdot = (mxdot + 1) % n;  
    while (dot(p[(mndot + 1) % n], cur) <= dot(p[mndot], cur))  
      mndot = (mndot + 1) % n;  
    Line l(p[i], p[(i + 1) % n]);  
    ld w = (dot(p[mxdot], cur) / abs(cur) - dot(p[mndot], cur) / abs(cur));  
    ld h = l.dist(p[j]);  
    perim = min(perim, 2.0 * (w + h));  
    area = min(area, w * h);  
    i++;  
  }  
  return {area, perim};  
}
```