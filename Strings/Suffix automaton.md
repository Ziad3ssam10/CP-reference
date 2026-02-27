```cpp
#include <bits/stdc++.h>
#define ll long long
#define ld long double
using namespace std;
const ll N = 1e5 + 10, LG = 20, mod = 1e9 + 7;
struct SuffixAutomaton
{
    int last = 0, cntState = 1;
    struct state
    {
        int len = 0, link = -1, cnt = 0, first_pos = 0;
        map<char, int> next;
    };
    state st[N * 2];
    ll dp[N * 2];
    void addChar(char c)
    {
        int p = last;
        int cur = cntState++;
        st[cur].len = st[last].len + 1;
        st[cur].cnt = 1;
        st[cur].first_pos = st[cur].len - 1;
        while(p != -1 && st[p].next.count(c) == 0)
        {
            st[p].next[c] = cur;
            p = st[p].link;
        }
        if(p == -1)
        {
            st[cur].link = 0;
        }
        else
        {
            int q = st[p].next[c];
            if(st[q].len == st[p].len + 1)
            {
                st[cur].link = q;
            }
            else
            {
                int clone = cntState++;
                st[clone].link = st[q].link;
                st[clone].len = st[p].len + 1;
                st[q].link = st[cur].link = clone;
                st[clone].next = st[q].next;
                st[clone].cnt = 0;
                st[clone].first_pos = st[q].first_pos;
                while(p != -1 && st[p].next[c] == q)
                {
                    st[p].next[c] = clone;
                    p = st[p].link;
                }
            }
        }
        last = cur;
    }
    bool checkForOccurrence(string str)
    {
        int cur = 0;
        for(int i = 0; i < str.size(); i++)
        {
            if(st[cur].next.count(str[i]))
            {
                cur = st[cur].next[str[i]];
            }
            else
                return false;
        }
        return true;
    }
    ll numberOfSubstrings()
    {
        ll ans = 0;
        for(int i = 1; i < cntState; i++)
        {
            ans += st[i].len - st[st[i].link].len;
        }
        return ans;
    }
    ll lengthOfSubstrings()
    {
        auto getSum = [&](ll x) -> ll {
            return x * (x + 1) / 2;
        };
        ll ans = 0;
        for(int i = 1; i < cntState; i++)
        {
            ans += getSum(st[i].len) - getSum(st[st[i].link].len);
        }
        return ans;
    }
    void numberOfOccPreProcess()
    {
        static bool visisted = false;
        if(visisted)return;
        visisted = true;
        vector<pair<int, int>> v;
        for(int i = 0; i < cntState; i++)
            v.push_back(pair<int, int>(st[i].len, i));
        sort(v.rbegin(), v.rend());
        for(int i = 0; i < cntState; i++)
        {
            if(st[v[i].second].link > 0)
                st[st[v[i].second].link].cnt += st[v[i].second].cnt;
            dp[v[i].second] = st[v[i].second].cnt;
            for(auto x: st[v[i].second].next)
            {
                dp[v[i].second] += dp[x.second];
            }
        }
    }
    ll numberOfOcc(string s)
    {
        numberOfOccPreProcess();
        int cur = 0;
        for(char c: s)
        {
            if(st[cur].next.count(c))
            {
                cur = st[cur].next[c];
            }
            else
                return 0;
        }
        return st[cur].cnt;
    }
    void dfs(int node, ll &k, string &ans)
    {
        k -= st[node].cnt;
        if(k <= 0)return;
        for(auto x: st[node].next)
        {
            if(dp[x.second] < k)
            {
                k -= dp[x.second];
            }
            else
            {
                ans.push_back(x.first);
                dfs(x.second, k, ans);
                return;
            }
        }
    }
    
    string getKthLex(ll k)
    {
        numberOfOccPreProcess();
        if(dp[0] < k)
            return "No such line.";
        string ans = "";
        dfs(0, k, ans);
        return ans;
    }
};
string lcs (string S, string T) {
    SuffixAutomaton sf;
    for (int i = 0; i < S.size(); i++)
        sf.addChar(S[i]);
    
    int v = 0, l = 0, best = 0, bestpos = 0;
    for (int i = 0; i < T.size(); i++) {
        while (v && !sf.st[v].next.count(T[i])) {
            v = sf.st[v].link ;
            l = sf.st[v].len;
        }
        if (sf.st[v].next.count(T[i])) {
            v = sf.st[v].next[T[i]];
            l++;
        }
        if (l > best) {
            best = l;
            bestpos = i;
        }
    }
    return T.substr(bestpos - best + 1, best);
}
void burn()
{
    string s;
    cin >> s;
    string x;
    x.resize(s.size());
    for(int i = 0; i < s.size(); i++)
    {
        if(s[i] == 'A')
            x[i] = 'T';
        else if(s[i] == 'T')
            x[i] = 'A';
        else if(s[i] == 'C')
            x[i] = 'G';
        else
            x[i] = 'C';
    }
    reverse(x.begin(), x.end());
    auto res = lcs(s, x);
    cout << res.size() << '\n';
    cout << res << '\n';
}

signed main()
{
    ios_base::sync_with_stdio(0), cin.tie(0), cout.tie(0);
    int t = 1;
    //  cin >> t;
    while (t--)
        burn();
}
```
