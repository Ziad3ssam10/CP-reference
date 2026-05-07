```cpp
int k;cin >> k;
int s[110];
int g[10000+10] = {};
for(int i = 0;i<k;i++)cin >> s[i];
for(int i = 1;i<=10000;i++){
    set<int>reach;
    for(int j = 0;j<k;j++){
        if(i-s[j] >= 0){
            reach.insert(g[i-s[j]]);
        }
    }
    for(auto ii : reach){
        if(g[i] == ii)g[i]++;
        else break;
    }
}




int m;cin >> m;
for(int i = 0;i<m;i++){
    int l;cin >> l;
    int ans = 0;
    for(int j = 0;j<l;j++){
        int x;cin >> x;
        ans ^= g[x];
    }
    if(!ans)cout << 'L';else cout << 'W';
}
```