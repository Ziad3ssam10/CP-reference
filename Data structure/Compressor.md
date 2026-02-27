```cpp
struct Compressor {  
    vector<int> vals;  
  
    void add(int x) {  
        vals.push_back(x);  
    }  
  
    void build() {  
        sort(vals.begin(), vals.end());  
        vals.erase(unique(vals.begin(), vals.end()), vals.end());  
    }  
  
    int get_id(int x) const {  
        return lower_bound(vals.begin(), vals.end(), x) - vals.begin();  
    }  
  
    int get_val(int idx) const {  
        return vals[idx];  
    }  
  
    int size() const {  
        return vals.size();  
    }  
};
```