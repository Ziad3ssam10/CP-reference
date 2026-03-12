### SOS DP 
```cpp
  // sum over subsets
  for (int i = 0; i < B; i++) {
    for (int mask = 0; mask < (1 << B); mask++) {
      if ((mask & (1 << i)) != 0) {
        f[mask] += f[mask ^ (1 << i)];
      }
    }
  }

  // sum over supersets
  for (int i = 0; i < B; i++) {
    for (int mask = (1 << B) - 1 ; mask >= 0 ; mask--) {
      if ((mask & (1 << i)) == 0) g[mask] += g[mask ^ (1 << i)] ;
    }
  }
```

### DP on profiles 
```cpp
  // sum over subsets
  for (int i = 0; i < B; i++) {
    for (int mask = 0; mask < (1 << B); mask++) {
      if ((mask & (1 << i)) != 0) {
        f[mask] += f[mask ^ (1 << i)];
      }
    }
  }

  // sum over supersets
  for (int i = 0; i < B; i++) {
    for (int mask = (1 << B) - 1 ; mask >= 0 ; mask--) {
      if ((mask & (1 << i)) == 0) g[mask] += g[mask ^ (1 << i)] ;
    }
  }
```