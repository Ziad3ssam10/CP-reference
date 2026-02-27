```cpp
struct BinaryTrie {
    struct Node {
        int child[ALPHA] = {};
        int f = 0;
    };

    vector<Node> trie;

    BinaryTrie() {
        trie.emplace_back();
    }

    void insert(int x) {
        int node = 0;
        for(int bit = LG; bit >= 0; bit--) {
            int val = (((1<<bit)&x) != 0);
            if(!trie[node].child[val]) {
                trie[node].child[val] = trie.size();
                trie.emplace_back();
            }
            node = trie[node].child[val];
            trie[node].f++;
        }
    }

    void erase(int x) {
        int node = 0;
        for(int bit = LG; bit >= 0; bit--) {
            int val = (((1<<bit)&x) != 0);

            node = trie[node].child[val];
            trie[node].f--;
        }
    }

    int query(int x) {
        int node = 0;
        int ret = 0;
        for(int bit = LG; bit >= 0; bit--) {
            int val = (((1<<bit)&x) == 0);
            if(trie[trie[node].child[val]].f)
                ret |= (1<<bit);
            else
                val ^= 1;

            node = trie[node].child[val];
            trie[node].f++;
        }
    }
};
```