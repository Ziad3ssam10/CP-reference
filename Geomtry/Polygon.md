```cpp
ld areaTriangle(pt a, pt b, pt c)  
{  
  return abs(cross(b - a, c - a)) / 2.0;  
}  
  
ld perimeter(vector<point > &p) {  
    ld ans = 0;  
    int n = p.size();  
    for (int i = 0; i < n; i++) ans += dist(p[i] - p[(i + 1) % n]);  
    return ans;  
}
T areaPolygon(vector<pt> &p)  
{  
  T area = 0;  
  for (int i = 0, n = p.size(); i < n; i++)  
  {  
    area += cross(p[i], p[(i + 1) % n]); // wrap back to 0 if i == n - 1  
  }  
  return abs(area) / 2.0;  
}  
  
bool above(pt a, pt p)  
{  
  return p.Y >= a.Y;  
}  
// check if [PQ] crosses ray from A  
bool crossesRay(pt a, pt p, pt q)  
{  
  return (above(a, q) - above(a, p)) * orient(a, p, q) > 0;  
}  
  // O(N) check to see if point a is inside a general polygon using ray-casting
bool inPolygon(vector<pt> p, pt a, bool &onBoundary, bool strict = true) // strict false will count border points also  
{  
  int numCrossings = 0;  
  for (int i = 0, n = p.size(); i < n; i++)  
  {  
    if (onSegment(p[i], p[(i + 1) % n], a))  
    {  
      onBoundary = true;  
      return !strict;  
    }  
    numCrossings += crossesRay(a, p[i], p[(i + 1) % n]);  
  }  
  return (numCrossings & 1); // inside if odd number of crossings  
}  
  
// checks if a polygon is convex or not  
bool isConvex(vector<pt> p)  
{  
  bool hasPos = false, hasNeg = false;  
  int n = p.size();  
  for (int i = 0; i < n; ++i)  
  {  
    int sign = orient(p[i], p[(i + 1) % n], p[(i + 2) % n]);  
    hasPos |= (sign > 0);  
    hasNeg |= (sign < 0);  
  }  
  return !(hasPos & hasNeg);  
}  
  
// 1 -> point out of polygon  
// 0 -> point on border of polygon  
// -1 -> point inside polygon  
// call prepareConvexPolygon(v) before it  
int inPolygonlg(pt pp, vector<pt> &v) // O(log(N))  
{  
  int n = v.size();  
  int o1 = orient(v[0], v[1], pp);  
  int o2 = orient(v[0], v[n - 1], pp);  
  if (o1 == 0 && onSegment(v[0], v[1], pp))  
    return 0;  
  else if (o2 == 0 && onSegment(v[0], v[n - 1], pp))  
    return 0;  
  else if (o1 < 0 || o2 > 0)  
    return 1;  
  int l = 0, r = n - 1;  
  int ans = 1;  
  while (l <= r)  
  {  
    int mid = l + (r - l) / 2;  
    int o = orient(v[0], v[mid], pp);  
    if (o > 0)  
    {  
      ans = mid;  
      l = mid + 1;  
    }  
    else  
    {  
      r = mid - 1;  
    }  
  }  
  int oo = orient(v[ans], v[(ans + 1) % n], pp);  
  if (oo > 0)  
    return -1;  
  else if (oo == 0 && onSegment(v[ans], v[(ans + 1) % n], pp))  
    return 0;  
  return 1;  
}  
  
// minimum distance between 2 polygons  
ld polyPoly(vector<pt> p1, vector<pt> p2)  
{  
  ld ret = 1e18;  
  auto lt = [&](pt a, pt b) -> bool  
  { return make_pair(a.X, a.Y) < make_pair(b.X, b.Y); };  
  auto gt = [&](pt a, pt b) -> bool  
  { return make_pair(a.X, a.Y) > make_pair(b.X, b.Y); };  
  auto nxt = [&](int a, int sz) -> int  
  { return (a + 1) % sz; };  
  auto prv = [&](int a, int sz) -> int  
  { return (a - 1 + sz) % sz; };  
  for (int rep = 0; rep < 2; ++rep)  
  {  
    swap(p1, p2);  
    int ptr1 = 0, ptr2 = 0, n = p1.size(), m = p2.size();  
    for (int i = 1; i < n; ++i)  
    {  
      if (lt(p1[i], p1[ptr1]))  
        ptr1 = i;  
    }  
    for (int i = 1; i < m; ++i)  
    {  
      if (gt(p2[i], p2[ptr2]))  
        ptr2 = i;  
    }  
    int cnt = 0;  
    for (; ptr1 < n; ptr1 = (ptr1 + 1) % n)  
    {  
      if (cnt == n)  
        break;  
      cnt++;  
      pt base = p1[nxt(ptr1, n)] - p1[ptr1];  
      Line l(p1[nxt(ptr1, n)], p1[ptr1]);  
      while (sgn(cross(base, p2[nxt(ptr2, m)] - p2[ptr2])) == sgn(cross(base, p2[ptr2] - p2[prv(ptr2, m)])))  
      {  
        ptr2 = nxt(ptr2, m);  
      }  
      ret = min(ret, segSeg(p1[nxt(ptr1, n)], p1[ptr1], p2[nxt(ptr2, m)], p2[ptr2]));  
      ret = min(ret, segSeg(p1[nxt(ptr1, n)], p1[ptr1], p2[ptr2], p2[prv(ptr2, m)]));  
    }  
  }  
  return ret;  
}

point polygonCenteriod(vector<point > points) {  
    ld x = 0, y = 0, a = 0, c;  
    for (int i = 0; i < points.size(); i++) {  
        int j = (i + 1) % points.size();  
        c = cross(points[i], points[j]), a += c;  
        x += (points[i].X + points[j].X) * c;  
        y += (points[i].Y + points[j].Y) * c;  
    }  
    if (dcmp(a, 0) == 0)return (points[0] + points.back()) * 0.5l;  
    a /= 2, x /= 6 * a, y /= 6 * a;  
    return point(x, y);  
}


```