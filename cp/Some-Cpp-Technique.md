# Some C++ Technique

## Custom Hash Map
- gp_hash_table is much faster than unordered_map or map
```cpp
#include <ext/pb_ds/assoc_container.hpp>
using namespace __gnu_pbds;
struct my_hash {
    const uint64_t RANDOM = chrono::steady_clock::now().time_since_epoch().count();
    static uint64_t splitmix64(uint64_t x) {
        x += 0x9e3779b97f4a7c15;
        x = (x ^ (x >> 30)) * 0xbf58476d1ce4e5b9;
        x = (x ^ (x >> 27)) * 0x94d049bb133111eb;
        return x ^ (x >> 31);
    }
    size_t operator()(uint64_t x) const {
        return splitmix64(x + RANDOM);
    }
};

int main() {
    gp_hash_table<long long,int,my_hash> dp;
}
```

## Uniquify Array
```cpp
vector<int> divisors = {1,2,3,3,2,2};
sort(divisors.begin(), divisors.end());
// unique will try to rearrange array, put the contiguos repeated elements to the last of the array, and return the last iterator
divisors.resize(unique(divisors.begin(), divisors.end())-divisors.begin());
for(int divisor : divisors) cout << divisor << " "; // 1,2,3
```

## Split
``` cpp
#include <bits/stdc++.h>
using namespace std;
#define ll long long
#define ar array

vector<string> tokenize(string const &str, const string delim) {
    size_t start;
    size_t end = 0;
    vector<string> out;
    while ((start = str.find_first_not_of(delim, end)) != std::string::npos) {
        end = str.find(delim, start);
        out.push_back(str.substr(start, end - start));
    }
    return out;
}
 

int main() {
    string s = "as bb cc";
    auto tokens = tokenize(s, " ");
    for(auto token : tokens) cout << token << " "; // aa bb cc 
}
```

## Recursive Lambda
``` cpp
int main() {
    int n;
    vector<vector<int>> adj(n, vector<int>());
    auto dfs = [&](auto &self, int u) -> void {
        for(auto v : adj[u]) {
            if(!vis[v]) self(self, v);
        }
    }
    dfs(dfs, 0);
}
```

## Pass Captured Lambda to function
``` cpp
int main() {
    int mode;
    auto cmp = [&](int lhs, int rhs) {
        if(mode==0) return lhs < rhs;
        else return rhs < lhs;
    };
    mode = 1;
    set<int, decltype(cmp)> a(cmp);
    for(int i = 0; i < 5; i++) a.insert(i);
    dbg(a); // 4,3,2,1,0
    mode = 0;
    set<int, decltype(cmp)> b(cmp);
    for(int i = 0; i < 5; i++) b.insert(i);
    dbg(b); // 0,1,2,3,4
}
```

## Others
### Find smallest 2 values in array
``` cpp
vector<int> as = {4, 5, 3, 7, 1, 2};
const int MAX = 2e9;
int mn2 = MAX , mn1 = MAX;
for(int a : as) {
    mn2 = min(mn2, a);
    if(mn2<mn1) swap(mn1, mn2);
}
// mn1 == 1, mn2 == 2;
```
