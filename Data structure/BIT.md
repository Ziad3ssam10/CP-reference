```cpp
struct FenwickTree {
    vector<int> bit;  // binary indexed tree
    int n;
     // 1 based 
    FenwickTree(int n) {
        this->n = n;
        bit.assign(n, 0);
    }

    FenwickTree(vector<int> const &a) : FenwickTree(a.size()) {
        for (size_t i = 0; i < a.size(); i++)
            add(i, a[i]);
    }

    int sum(int r) {
        int ret = 0;
        for (; r >= 0; r = (r & (r + 1)) - 1)
            ret += bit[r];
        return ret;
    }

    int sum(int l, int r) {
        return sum(r) - sum(l - 1);
    }

    void add(int idx, int delta) {
        for (; idx < n; idx = idx | (idx + 1))
            bit[idx] += delta;
    }
    
    // This is equivalent to calculating lower_bound on prefix sums array
// LOGN = log(N)

int bit_search(int v)
{
	int sum = 0;
	int pos = 0;
	
	for(int i=LOGN; i>=0; i--)
	{
		if(pos + (1 << i) < N and sum + bit[pos + (1 << i)] < v)
		{
			sum += bit[pos + (1 << i)];
			pos += (1 << i);
		}
	}

	return pos + 1; // +1 because 'pos' will have position of largest value less than 'v'
}
};



```