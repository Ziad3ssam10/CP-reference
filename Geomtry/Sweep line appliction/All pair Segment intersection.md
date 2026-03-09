 ```cpp
const ld EPS = 1e-11;  
  
struct Point {  
    ld x, y;  
};  
  
struct Segment {  
    Point p, q;  
    int id;  
  
    ld get_y(ld x) const {  
        if (abs(p.x - q.x) < EPS) return p.y;  
        return p.y + (q.y - p.y) * (x - p.x) / (q.x - p.x);  
    }  
};  
  
ld sweep_x;  
  
bool operator<(const Segment &a, const Segment &b) {  
    if (a.id == b.id) return false;  
    ld ya = a.get_y(sweep_x);  
    ld yb = b.get_y(sweep_x);  
    if (abs(ya - yb) > EPS) return ya < yb;  
    return a.id < b.id;  
}  
  
ld cross_product(Point a, Point b, Point c) {  
    return (b.x - a.x) * (c.y - a.y) - (b.y - a.y) * (c.x - a.x);  
}  
  
bool on_segment(Point p, Segment s) {  
    return p.x >= min(s.p.x, s.q.x) - EPS && p.x <= max(s.p.x, s.q.x) + EPS &&  
           p.y >= min(s.p.y, s.q.y) - EPS && p.y <= max(s.p.y, s.q.y) + EPS;  
}  
  
bool intersect(Segment s1, Segment s2) {  
    ld d1 = cross_product(s1.p, s1.q, s2.p);  
    ld d2 = cross_product(s1.p, s1.q, s2.q);  
    ld d3 = cross_product(s2.p, s2.q, s1.p);  
    ld d4 = cross_product(s2.p, s2.q, s1.q);  
  
    if (((d1 > EPS && d2 < -EPS) || (d1 < -EPS && d2 > EPS)) &&  
        ((d3 > EPS && d4 < -EPS) || (d3 < -EPS && d4 > EPS)))  
        return true;  
  
    if (abs(d1) < EPS && on_segment(s2.p, s1)) return true;  
    if (abs(d2) < EPS && on_segment(s2.q, s1)) return true;  
    if (abs(d3) < EPS && on_segment(s1.p, s2)) return true;  
    if (abs(d4) < EPS && on_segment(s1.q, s2)) return true;  
  
    return false;  
}  
  
struct Event {  
    ld x;  
    int type;  
    int id;  
  
    bool operator<(const Event &other) const {  
        if (abs(x - other.x) > EPS) return x < other.x;  
        return type > other.type;  
    }  
};  
  
bool has_intersection(vector<Segment> &segments) {  
    int n = segments.size();  
    vector<Event> events;  
    for (int i = 0; i < n; i++) {  
        if (segments[i].p.x > segments[i].q.x) swap(segments[i].p, segments[i].q);  
        events.push_back({segments[i].p.x, 1, i});  
        events.push_back({segments[i].q.x, -1, i});  
    }  
    sort(events.begin(), events.end());  
  
    set<Segment> active;  
    vector<set<Segment>::iterator> iters(n);  
  
    for (auto &e: events) {  
        sweep_x = e.x;  
        if (e.type == 1) {  
            auto it = active.lower_bound(segments[e.id]);  
            if (it != active.end() && intersect(segments[e.id], *it)) return true;  
            if (it != active.begin() && intersect(segments[e.id], *prev(it))) return true;  
            iters[e.id] = active.insert(it, segments[e.id]);  
        } else {  
            auto it = iters[e.id];  
            auto nxt = next(it), prv = (it == active.begin() ? active.end() : prev(it));  
            if (nxt != active.end() && prv != active.end() && intersect(*nxt, *prv)) return true;  
            active.erase(it);  
        }  
    }  
    return false;  
}
 ```