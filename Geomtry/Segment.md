```cpp
bool inDisk(pt a, pt b, pt p)  
{  
    return dot(a - p, b - p) <= EPS;  
}  
  
bool onSegment(pt a, pt b, pt c) // check if c on ab segment  
{  
    return orient(a, b, c) == 0 && inDisk(a, b, c);  
}  
  // Finds the intersection of segments ab and cd
bool properInter(pt a, pt b, pt c, pt d, pt &out)  
{  
    T oa = orient(c, d, a),  
      ob = orient(c, d, b),  
      oc = orient(a, b, c),  
      od = orient(a, b, d);  
    // Proper intersection exists iff opposite signs  
    if (sgn(oa) * sgn(ob) < 0 && sgn(oc) * sgn(od) < 0)  
    {  
        out = (a * ob - b * oa) / (ob - oa);  
        return true;  
    }  
    return false;  
}  
  // Returns a set of all intersection points between segments ab and cd
set<pair<ld, ld>> inters(pt a, pt b, pt c, pt d)  
{  
    set<pair<ld, ld>> s;  
    pt out;  
    if (a == c || a == d)  
    {  
        s.insert(make_pair(a.X, a.Y));  
    }  
    if (b == c || b == d)  
    {  
        s.insert(make_pair(b.X, b.Y));  
    }  
    if (s.size())  
        return s;  
  
    if (properInter(a, b, c, d, out))  
        return {make_pair(out.X, out.Y)};  
    if (onSegment(c, d, a))  
        s.insert(make_pair(a.X, a.Y));  
    if (onSegment(c, d, b))  
        s.insert(make_pair(b.X, b.Y));  
    if (onSegment(a, b, c))  
        s.insert(make_pair(c.X, c.Y));  
    if (onSegment(a, b, d))  
        s.insert(make_pair(d.X, d.Y));  
  
    return s;  
}  
  // Calculates the shortest distance from point p to segment ab and identifies the closest point 'out
ld segPoint(pt a, pt b, pt p, pt &out)  
{  
    if (a != b)  
    {  
        Line l(a, b);  
        if (l.cmpProj(a, p) and l.cmpProj(p, b)) // if closest to projection (in between a & b)  
        {  
            out = l.proj(p);  
            return l.dist(p); // output distance to line  
        }  
    }  
    if (abs(p - a) - abs(p - b) > EPS)  
        out = b;  
    else  
        out = a;  
    return min(abs(p - a), abs(p - b)); // otherwise distance to A or B  
}  
  // Calculates the minimum distance between two segments ab and cd
ld segSeg(pt a, pt b, pt c, pt d)  
{  
    pt dummy;  
    if (properInter(a, b, c, d, dummy))  
        return 0;  
    return min({segPoint(a, b, c, dummy), segPoint(a, b, d, dummy),  
                segPoint(c, d, a, dummy), segPoint(c, d, b, dummy)});  
}
```