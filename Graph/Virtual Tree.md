```cpp
// If you have some k nodes, and you want a tree such that is compressed,  

// the tree contains only the nodes is important to all paths (all pairs lca + k nodes)  

// There at most k-1 distinct LCAs, If you sort by timer,  

// Each 2 nodes will be merged into lca and be 1 group, only consider LCA(v[i], v[i+1)  

// Now we have all nodes and lca  

// To construct the virtual tree only use a stack that the top of the stack is the current parent  

// We try to make v[i] is child of this parent, so we sort them in time and push them in the stack in that order  

// It's monotonic stack approach, If the top of the stack can't be ancestor of current v[i] node, pop it (never be a parent again)  

// Tested
vector<int> virtual_tree(vector<int> v) {  

    sort(v.begin(), v.end(), [&](int u, int v) -> bool {  

        return in[u] < in[v];  

    });  

    vector<int> temp = v;  

    for (int i = 1; i < v.size(); ++i)  

        temp.push_back(lca(v[i], v[i - 1]));  

    v = move(temp);  

    sort(v.begin(), v.end(), [&](int u, int v) -> bool {  

        return in[u] < in[v];  

    });  

    v.erase(unique(v.begin(), v.end()), v.end());  

    stack<int> st;  

    st.push(v[0]);  

    for (int i = 1; i < v.size(); ++i) {  

        while (!is_ancestor(st.top(), v[i])) st.pop();  

        vir[st.top()].push_back({v[i], lvl[v[i]] - lvl[st.top()]});  

        st.push(v[i]);  

    }  

    return v;  

}
```