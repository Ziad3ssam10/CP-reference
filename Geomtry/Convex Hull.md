```cpp
vector<pt> lower_hull(vector<pt> &points) {  
    // make sure points are sorted with cmp  
    vector<pt> lower;  
    for (auto &p: points) {  
        while (lower.size() > 1 && orient(lower[lower.size() - 2], lower.back(), p) <= 0) // < to include collinear  
        {  
            lower.pop_back();  
        }  
        lower.push_back(p);  
    }  
    return lower;  
}  
  
vector<pt> upper_hull(vector<pt> &points) {  
    // make sure points are sorted with cmp  
    vector<pt> upper;  
    for (auto &p: points) {  
        while (upper.size() > 1 && orient(upper[upper.size() - 2], upper.back(), p) >= 0) // > to include collinear  
        {  
            upper.pop_back();  
        }  
        upper.push_back(p);  
    }  
    return upper;  
}  
  
vector<pt> convex_hull(vector<pt> &p) {  
    if (p.size() <= 1)  
        return p;  
  
    vector<pt> sorted = p;  
    sort(all(sorted), cmp);  
  
    vector<pt> lower = lower_hull(sorted);  
    vector<pt> upper = upper_hull(sorted);  
  
    // Remove duplicate endpoints  
    lower.pop_back();  
    reverse(upper.begin(), upper.end());  
    upper.pop_back();  
  
    // Combine  
    lower.insert(lower.end(), upper.begin(), upper.end());  
  
    if (lower.size() == 2 && lower[0] == lower[1])  
        lower.pop_back();  
  
    return lower;  
}
```

### other implementation 

```cpp
struct Line {  
    point a, b;  
};  
   
ld dist(point a,point b) {  
    return abs(cross(b - a, a)) / abs(b - a);  
}  
   
bool ccw(point a, point b, point c) {  
    return cross(b - a, c - b) > 0;  
}  
   
void convex_hull(vector<point > &a, vector<point > &h) {  
    sort(a.begin(), a.end(), [](point p1, point p2) {  
        if (!eq(real(p1), real(p2))) return real(p1) < real(p2);  
        return imag(p1) < imag(p2);  
    });  
    vector<point > stk;  
    for (auto &p: a) {  
        while (stk.size() >= 2 and !ccw(stk[stk.size() - 2], stk[stk.size() - 1], p)) stk.pop_back();  
        stk.push_back(p);  
    }  
    int tmp = stk.size();  
    for (int i = (int) a.size() - 2; i >= 0; i--) {  
        point p = a[i];  
        while (stk.size() > tmp and !ccw(stk[stk.size() - 2], stk[stk.size() - 1], p)) stk.pop_back();  
        stk.push_back(p);  
    }  
    h = stk;  
}
```