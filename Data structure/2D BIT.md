```cpp
#include <iostream>
#include <vector>
using namespace std;

/**
 * 2D Fenwick Tree implementation.
 * Note that all cell locations are zero-indexed
 * in this implementation.
 */
template <typename T> class BIT2D {
  private:
	const int n, m;
	vector<vector<T>> bit;

  public:
	BIT2D(int n, int m) : n(n), m(m), bit(n + 1, vector<T>(m + 1)) {}

	/** adds val to the point (r, c) */
	void add(int r, int c, T val) {
		r++, c++;
		for (; r <= n; r += r & -r) {
			for (int i = c; i <= m; i += i & -i) { bit[r][i] += val; }
		}
	}

	/** @returns sum of points with row in [0, r] and column in [0, c] */
	T rect_sum(int r, int c) {
		r++, c++;
		T sum = 0;
		for (; r > 0; r -= r & -r) {
			for (int i = c; i > 0; i -= i & -i) { sum += bit[r][i]; }
		}
		return sum;
	}

	/** @returns sum of points with row in [r1, r2] and column in [c1, c2] */
	T rect_sum(int r1, int c1, int r2, int c2) {
		return rect_sum(r2, c2) - rect_sum(r2, c1 - 1) - rect_sum(r1 - 1, c2) +
		       rect_sum(r1 - 1, c1 - 1);
	}
};

int main() {
	int n, q;
	cin >> n >> q;
	BIT2D<int> bit(n, n);
	for (int i = 0; i < n; i++) {
		for (int j = 0; j < n; j++) {
			char c;
			cin >> c;
			if (c == '*') { bit.add(i, j, 1); }
		}
	}

	for (int i = 0; i < q; i++) {
		int type;
		cin >> type;
		if (type == 1) {
			int r, c;
			cin >> r >> c;
			r--, c--;
			if (bit.rect_sum(r, c, r, c) == 1) {
				bit.add(r, c, -1);
			} else {
				bit.add(r, c, 1);
			}
		} else if (type == 2) {
			int r1, c1, r2, c2;
			cin >> r1 >> c1 >> r2 >> c2;
			r1--, c1--, r2--, c2--;
			cout << bit.rect_sum(r1, c1, r2, c2) << '\n';
		}
	}
}
```

#### range update 
```cpp
/**  
 * 2D Fenwick Tree (RURQ) implementation. * API is 0-indexed. Internals are 1-indexed. * Note: Use long long for T to prevent overflow from coordinate multiplication. */template <typename T> class BIT2D_RURQ {  
private:  
    int n, m;  
    vector<vector<T>> bit1, bit2, bit3, bit4;  
  
    // Internal 1-indexed point update  
    void point_add(int r, int c, T val) {  
        for (int i = r; i <= n; i += i & -i) {  
            for (int j = c; j <= m; j += j & -j) {  
                bit1[i][j] += val;  
                bit2[i][j] += val * r;  
                bit3[i][j] += val * c;  
                bit4[i][j] += val * r * c;  
            }  
        }  
    }  
  
    // Internal 1-indexed prefix sum  
    T prefix_sum(int r, int c) {  
        T sum = 0;  
        for (int i = r; i > 0; i -= i & -i) {  
            for (int j = c; j > 0; j -= j & -j) {  
                sum += bit1[i][j] * (r + 1) * (c + 1)   
                     - bit2[i][j] * (c + 1)   
                     - bit3[i][j] * (r + 1)   
                     + bit4[i][j];  
            }  
        }  
        return sum;  
    }  
  
public:  
    BIT2D_RURQ(int n, int m) : n(n), m(m),   
        bit1(n + 1, vector<T>(m + 1, 0)),  
        bit2(n + 1, vector<T>(m + 1, 0)),  
        bit3(n + 1, vector<T>(m + 1, 0)),  
        bit4(n + 1, vector<T>(m + 1, 0)) {}  
  
    /** Adds val to the rectangle bounded by (r1, c1) and (r2, c2) (0-indexed) */  
    void range_add(int r1, int c1, int r2, int c2, T val) {  
        r1++; c1++; r2++; c2++; // Shift to 1-indexed  
        point_add(r1, c1, val);  
        point_add(r2 + 1, c1, -val);  
        point_add(r1, c2 + 1, -val);  
        point_add(r2 + 1, c2 + 1, val);  
    }  
  
    /** @returns sum of points with row in [r1, r2] and column in [c1, c2] (0-indexed) */  
    T rect_sum(int r1, int c1, int r2, int c2) {  
        r1++; c1++; r2++; c2++; // Shift to 1-indexed  
        return prefix_sum(r2, c2)   
             - prefix_sum(r1 - 1, c2)   
             - prefix_sum(r2, c1 - 1)   
             + prefix_sum(r1 - 1, c1 - 1);  
    }  
};
```

``` cpp
## Xor version 
#include <iostream>
#include <vector>

using namespace std;

/**
 * 2D Fenwick Tree (Range XOR Update, Range XOR Query).
 * API is 0-indexed. Internals are 1-indexed.
 */
template <typename T> class BIT2D_XOR {
private:
    int n, m;
    vector<vector<T>> bit[2][2];

    void point_xor(int r, int c, T val) {
        int r_par = r % 2;
        int c_par = c % 2;
        for (int i = r; i <= n; i += i & -i) {
            for (int j = c; j <= m; j += j & -j) {
                bit[r_par][c_par][i][j] ^= val;
            }
        }
    }

    T prefix_xor(int r, int c) {
        if (r < 1 || c < 1) return 0;
        T res = 0;
        int r_par = r % 2;
        int c_par = c % 2;
        for (int i = r; i > 0; i -= i & -i) {
            for (int j = c; j > 0; j -= j & -j) {
                res ^= bit[r_par][c_par][i][j];
            }
        }
        return res;
    }

public:
    BIT2D_XOR(int n, int m) : n(n), m(m) {
        for (int i = 0; i < 2; i++) {
            for (int j = 0; j < 2; j++) {
                bit[i][j].assign(n + 1, vector<T>(m + 1, 0));
            }
        }
    }

    /** XORs val into the rectangle bounded by (r1, c1) and (r2, c2) (0-indexed) */
    void range_xor(int r1, int c1, int r2, int c2, T val) {
        r1++; c1++; r2++; c2++; // Shift to 1-indexed
        point_xor(r1, c1, val);
        point_xor(r2 + 1, c1, val);
        point_xor(r1, c2 + 1, val);
        point_xor(r2 + 1, c2 + 1, val);
    }

    /** @returns XOR sum of points with row in [r1, r2] and column in [c1, c2] (0-indexed) */
    T rect_xor(int r1, int c1, int r2, int c2) {
        r1++; c1++; r2++; c2++; 
        return prefix_xor(r2, c2) 
             ^ prefix_xor(r1 - 1, c2) 
             ^ prefix_xor(r2, c1 - 1) 
             ^ prefix_xor(r1 - 1, c1 - 1);
    }
};

int main() {
    // Fast I/O
    ios_base::sync_with_stdio(0);
    cin.tie(0); cout.tie(0);

    int n, q;
    cin >> n >> q;
    BIT2D_XOR<int> bit(n, n);

    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            char c;
            cin >> c;
            if (c == '*') { 
                bit.range_xor(i, j, i, j, 1); 
            }
        }
    }

    for (int i = 0; i < q; i++) {
        int type;
        cin >> type;
        
        if (type == 1) {
            int r1, c1, r2, c2, val;
            cin >> r1 >> c1 >> r2 >> c2 >> val;
            r1--, c1--, r2--, c2--; 
            bit.range_xor(r1, c1, r2, c2, val);
            
        } else if (type == 2) {
            int r1, c1, r2, c2;
            cin >> r1 >> c1 >> r2 >> c2;
            r1--, c1--, r2--, c2--; 
            cout << bit.rect_xor(r1, c1, r2, c2) << '\n';
        }
    }
    return 0;
}
```
