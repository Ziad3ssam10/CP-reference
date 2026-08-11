```cpp
// returns (0,0) for null path
pair<int, int> getCommonPath(int u, int a, int v, int b) { // returns common path between u->a and v->b where u is ancestor a and v is ancestor b
  if ( !isanc(a, v)) return make_pair(0, 0); // v has to be an ancestor of a to have any common path
  int x = lca(a, b);
  if (dep[v] < dep[u]) { // v is an ancestor of u, so the beginning of common path should be from u
    if (isanc(x, u)) // if u is not an ancestor of x no common path exists
      return make_pair(u, x); // u->x is the common path
  } else { // u is an ancestor of v, so the beginning of common path should be from v
    if (isanc(x, v)) // if v is not an ancestor of x no common path exists
      return make_pair(v, x); // v->x is the common path
  }
  return make_pair(0, 0);
}
pair<int, int> path_union_light(pair<int, int> x, pair<int, int> y) {
  if (x.first == 0) return y; //where {0,0} is a null path
  if (y.first == 0) return x;
  vector<int> can = {x.first, x.second, y.first, y.second};
  int a = can[0];
  for (int u : can) if (dep[u] > dep[a]) a = u;
  int b = -1;
  for (int u : can)if (!isanc(a, u)) {
      if (b == -1) b = u;
      if (dep[b] < dep[u]) b = u;
    }
  if (b == -1) {
    b = can[0];
    for (int u : can) if (dep[u] < dep[b]) b = u;
    return {a, b};
  }
  return {a, b};
}
// returns the intersection of two paths (a,b) and (c,d)
// {0,0} for null path
pair<int, int> path_intersection(int a, int b, int c, int d) {
  if (a == 0 || b == 0) return {0, 0};
  int u = lca(a, b), v = lca(c, d); // splits both paths in two chains from lca to one node
  pair<int, int> x, y;
  x = getCommonPath(u, a, v, c);
  y = getCommonPath(u, a, v, d);
  pair<int, int> a1 = path_union_light(x, y);
  x = getCommonPath(u, b, v, c);
  y = getCommonPath(u, b, v, d);
  pair<int, int> a2 = path_union_light(x, y);
  return path_union_light(a1, a2);
}
```