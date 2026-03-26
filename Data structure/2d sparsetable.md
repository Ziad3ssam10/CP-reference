```cpp
struct SparseTable2D {  
    int n, m;  
    int max_log_n, max_log_m;  
    vector<vector<vector<vector<int>>>> table;  
   
    SparseTable2D(const vector<vector<int>>& grid) {  
        n = grid.size();  
        m = grid[0].size();  
        max_log_n = std::bit_width(1ull * n);  
        max_log_m = std::bit_width(1ull * m);  
   
        table.assign(max_log_n, vector<vector<vector<int>>>(max_log_m,  
                     vector<vector<int>>(n, vector<int>(m))));  
   
        // Base case: 1x1 blocks  
        for (int i = 0; i < n; i++) {  
            for (int j = 0; j < m; j++) {  
                table[0][0][i][j] = grid[i][j];  
            }  
        }  
   
        // Build for the first dimension (rows)  
        for (int ir = 0; ir < n; ir++) {  
            for (int jc = 1; jc < max_log_m; jc++) {  
                for (int ic = 0; ic + (1 << jc) <= m; ic++) {  
                    table[0][jc][ir][ic] = merge(table[0][jc - 1][ir][ic],  
                                                 table[0][jc - 1][ir][ic + (1 << (jc - 1))]);  
                }  
            }  
        }  
   
        // Build for the rest of the dimensions  
        for (int jr = 1; jr < max_log_n; jr++) {  
            for (int jc = 0; jc < max_log_m; jc++) {  
                for (int ir = 0; ir + (1 << jr) <= n; ir++) {  
                    for (int ic = 0; ic + (1 << jc) <= m; ic++) {  
                        table[jr][jc][ir][ic] = merge(table[jr - 1][jc][ir][ic],  
                                                      table[jr - 1][jc][ir + (1 << (jr - 1))][ic]);  
                    }  
                }  
            }  
        }  
    }  
   
    int merge(int a, int b) {  
        return max(a, b);  
    }  
   
    int query(int r1, int c1, int r2, int c2) {  
        int kr = std::bit_width(1ull * (r2 - r1 + 1)) - 1;  
        int kc = std::bit_width(1ull * (c2 - c1 + 1)) - 1;  
   
        int top_left = table[kr][kc][r1][c1];  
        int bottom_left = table[kr][kc][r2 - (1 << kr) + 1][c1];  
        int top_right = table[kr][kc][r1][c2 - (1 << kc) + 1];  
        int bottom_right = table[kr][kc][r2 - (1 << kr) + 1][c2 - (1 << kc) + 1];  
   
        return merge(merge(top_left, bottom_left), merge(top_right, bottom_right));  
    }  
};
```