```cpp
// Calculates the number of integer grid points lying on the line segment AB
int latticeAB(pt A, pt B)  
{  
    return gcd((int)abs(A.X - B.X), (int)abs(A.Y - B.Y)) + 1;  
}  
  // Applies Pick's Theorem to find the number of integer points inside and on the boundary of a polygon
// Pick's Theorem -> Area = (interior lattice points) + (boundary lattice points / 2) - 1  
// make T int  
pair<int, int> picks(vector<pt> &v) // {inside,boundary}  
{  
    int n = v.size();  
    ld area = 0;  
    int boundary = 0;  
    for (int i = 0; i < n; ++i)  
    {  
        area += cross(v[i], v[(i + 1) % n]);  
        pt vec = v[(i + 1) % n] - v[i];  
        boundary += gcd((int)vec.X, (int)vec.Y); // T has to be int  
    }  
    int in = (abs(area) - boundary + 2) / 2;  
    return make_pair(in, boundary);  
}
```