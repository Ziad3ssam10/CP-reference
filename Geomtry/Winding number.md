```cpp
/*  
the winding number of a closed curve in the plane around a given point  
is an integer representing the total number of times that the curve travels counterclockwise around the point  
*/  
// inf -> a is on edge of p  
// 0 -> a is outside p  
// otherwise -> a is inside p  
int winding_number(vector<pt> &p, pt a)  
{  
    int ret = 0;  
    for (int i = 0, n = p.size(); i < n; ++i)  
    {  
        pt cur = p[i];  
        pt nxt = p[(i + 1) % n];  
        if (onSegment(cur, nxt, a))  
            return inf;  
        if (cur.Y <= a.Y and nxt.Y > a.Y and cross(nxt - cur, a - cur) > 0)  
            ret++;  
        else if (cur.Y > a.Y and nxt.Y <= a.Y and cross(nxt - cur, a - cur) < 0)  
            ret--;  
    }  
    return ret;  
}
```