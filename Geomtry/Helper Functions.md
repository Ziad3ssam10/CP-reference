```cpp
vector<pt> remove_colinear(vector<pt> pts)  
{  
  vector<pt> use;  
  use.push_back(pts[0]);  
  use.push_back(pts[1]);  
  for (int i = 2; true; ++i)  
  {  
    pt a = use[(int)use.size() - 2];  
    pt b = use[(int)use.size() - 1];  
    if (sgn(cross(b - a, pts[i % (int)pts.size()] - a)) == 0)  
      use.pop_back();  
    if (i == (int)pts.size())  
      break;  
    use.push_back(pts[i]);  
  }  
  pt a = use[(int)use.size() - 1];  
  pt b = use[0];  
  pt c = use[1];  
  if (sgn(cross(b - a, c - a)) == 0)  
    use.erase(use.begin());  
  return use;  
}  
  
void prepareConvexPolygon(vector<pt> &v)  
{  
  // 1. Find the point with the lowest y (break ties by x)  
  int pivot = 0;  
  for (int i = 1; i < v.size(); ++i)  
    if (v[i].Y < v[pivot].Y || (v[i].Y == v[pivot].Y && v[i].X < v[pivot].X))  
      pivot = i;  
  
  // 2. Rotate the polygon so that pivot is at index 0  
  rotate(v.begin(), v.begin() + pivot, v.end());  
  
  // 3. Ensure polygon is CCW  
  if (orient(v[0], v[1], v[2]) < 0)  
    reverse(v.begin() + 1, v.end()); // Keep v[0], reverse rest  
}  
  
// rotate the polygon such that the (bottom, left)-most point is at the first position  
void reorder_polygon(vector<pt> &p)  
{  
  int pos = 0;  
  for (int i = 1; i < p.size(); i++)  
  {  
    if (p[i].Y < p[pos].Y || (sgn(p[i].Y - p[pos].Y) == 0 && p[i].X < p[pos].X))  
      pos = i;  
  }  
  rotate(p.begin(), p.begin() + pos, p.end());  
}  
  
bool point_in_triangle(pt a, pt b, pt c, pt p, bool strictly_in = false)  
{  
  int sign1 = cross(b - a, p - a);  
  int sign2 = cross(c - b, p - b);  
  int sign3 = cross(a - c, p - c);  
  if (strictly_in)  
    return ((sign1 > 0 and sign2 > 0 and sign3 > 0) or (sign1 < 0 and sign2 < 0 and sign3 < 0));  
  else  
    return ((sign1 >= 0 and sign2 >= 0 and sign3 >= 0) or (sign1 <= 0 and sign2 <= 0 and sign3 <= 0));  
}  
  
bool half(pt p) // true if in blue half  
{  
  assert(p.X != 0 || p.Y != 0); // the argument of (0,0) is undefined  
  return p.Y > 0 || (p.Y == 0 && p.X < 0);  
}  
void polarSort(vector<pt> &v)  
{  
  sort(v.begin(), v.end(), [](pt v, pt w)  
       { return make_tuple(half(v), 0) < make_tuple(half(w), cross(v, w)); });  
}


void sort_cw(vector<point > &p) {  
    int n = p.size();  
    point ctr = {0, 0};  
    for (auto &x: p) ctr += x;  
    ctr /= (ld) n;  
   
    sort(p.begin(), p.end(), [&](const point &a, const point &b) {  
        ld A = atan2(a.Y - ctr.Y, a.X - ctr.X);  
        ld B = atan2(b.Y - ctr.Y, b.X - ctr.X);  
        return A > B;  
    });  
}  
   
void sort_ccw(vector<point > &p) {  
    sort_cw(p);  
    reverse(p.begin(), p.end());  
}
```