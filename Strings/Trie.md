```cpp
const int ALPHA = 26;

struct Trie {
    struct Node {
        int child[ALPHA] = {};
        int f = 0;
    };

    vector<Node> trie;

    Trie() {
        trie.emplace_back();
    }

    void insert(string& s) {
        int node = 0;
        for(auto& i : s) {
            int chIdx = i-'a';
            if(!trie[node].child[chIdx]) {
                trie[node].child[chIdx] = trie.size();
                trie.emplace_back();
            }
            node = trie[node].child[chIdx];
            trie[node].f++;
        }
    }

    void erase(string& s) {
        int node = 0;
        for(auto& i : s) {
            int chIdx = i-'a';

            node = trie[node].child[chIdx];
            trie[node].f--;
        }
    }

    int query(string& s) {
        int node = 0;
        for(auto& i : s) {
            int chIdx = i-'a';
            if(!trie[trie[node].child[chIdx]].f)
                return 0;
            node = trie[node].child[chIdx];
            trie[node].f++;
        }
        return trie[node].f;
    }
};

```