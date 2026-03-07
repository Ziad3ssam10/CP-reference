```cpp
// Calculates the shortest distance between point p and a ray starting at a and passing through b
int pointRay(pt p, pt a, pt b) // a is ray's origin, b defines direction  
{  
    pt dir = b - a; // ray direction vector  
    pt v = p - a;   // vector from origin to point  
  
    T u = dot(v, dir);  
  
    if (u <= 0) // Point is behind the ray origin  
    {  
        return abs(v); // distance from p to a  
    }  
    else // Point is in front of the ray  
    {  
        // Compute closest point on the ray: a + (u / ||dir||²) * dir  
        pt closest = a + (u / norm(dir)) * dir;  
        return abs(p - closest); // distance from p to closest point  
    }  
}  
  // Calculates the minimum distance between two rays (ray ab and ray cd)
ld rayRay(pt a, pt b, pt c, pt d)  
{  
    T inf = 1e12;  
    b = a + (b - a) * inf;  
    d = c + (d - c) * inf;  
    if (inters(a, b, c, d).size())  
        return 0;  
    pt dummy;  
    return min({segPoint(a, b, c, dummy), segPoint(a, b, d, dummy), segPoint(c, d, a, dummy), segPoint(c, d, b, dummy)});  
}
```