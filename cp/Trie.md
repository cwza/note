# Trie
* https://www.youtube.com/watch?v=MyiHeqtwOWQ
## Problem 1
* https://leetcode.com/problems/implement-trie-prefix-tree/description/
``` cpp
#include <bits/stdc++.h>
using namespace std;
#ifdef DEBUG
    #include "./tool/debug.hpp"
#else
    #define dbg(...)
    #define dbgarr(a)
#endif
using ll = long long;

class Trie {
public:
    vector<vector<int>> nxt;
    vector<int> is_terminal;
    int id;
    Trie(): id(0) {
        nxt = vector<vector<int>>(1, vector<int>(26));
        is_terminal = vector<int>(1);
    }
    
    void insert(string word) {
        int cur = 0;
        for(char ch : word) {
            if(!nxt[cur][ch-'a']) {
                nxt[cur][ch-'a'] = ++id;
                nxt.push_back(vector<int>(26));
                is_terminal.push_back(0);
            }
            cur = nxt[cur][ch-'a'];
        }
        is_terminal[cur] = 1;
    }
    
    bool search(string word) {
        int cur = 0;
        for(char ch : word) {
            if(!nxt[cur][ch-'a']) return 0;
            cur = nxt[cur][ch-'a'];
        }
        return is_terminal[cur];
    }
    
    bool startsWith(string prefix) {
        int cur = 0;
        for(char ch : prefix) {
            if(!nxt[cur][ch-'a']) return 0;
            cur = nxt[cur][ch-'a'];
        }
        return 1;
    }
};

 
int main() {
    ios_base::sync_with_stdio(0);
    cin.tie(0);
 
    Trie trie;
    trie.insert("app");
    trie.insert("apple");
    trie.insert("beer");
    trie.insert("add");
    trie.insert("jam");
    trie.insert("rental");
    cout << trie.search("apps") << endl;
    cout << trie.search("app") << endl;
}
```

## Problem 2
* https://leetcode.com/problems/longest-word-in-dictionary/
``` cpp
#include<bits/stdc++.h>
using namespace std;
class Solution {
public:
    string longestWord(vector<string>& words) {
        vector<vector<int>> nxt = vector<vector<int>>(1, vector<int>(26));
        vector<int> is_terminal = vector<int>(1);
        int id = 0;
        auto insert = [&](string &s) -> void {
            int cur = 0;
            for(char ch : s) {
                if(!nxt[cur][ch-'a']) {
                    nxt[cur][ch-'a'] = ++id;
                    nxt.push_back(vector<int>(26));
                    is_terminal.push_back(0);
                }
                cur = nxt[cur][ch-'a'];
            }
            is_terminal[cur] = 1;
        };
        for(string &s : words) insert(s);

        string ans = "";
        auto dfs = [&](auto &self, int u, string &s) ->void {
            if(s.size()>ans.size()) ans = s;
            for(int i = 0; i < 26; i++) {
                int v = nxt[u][i];
                if(v && is_terminal[v]) {
                    s += char(i+'a');
                    self(self, v, s);
                    s.pop_back();
                }
            }
        };
        string s = "";
        dfs(dfs, 0, s);
        return ans;
    }
};
```

## Problem 3 - Maximum Xor Subarray
* https://cses.fi/problemset/result/11392614/
* Key:
    + The concept is very like maximum subarray sum
    + In maximum subarray sum we need to find the smallest one to make our sum large
    + But in xor we use trie to help us to find the one that make our xor large
``` cpp
#include <bits/stdc++.h>
using namespace std;
#ifdef DEBUG
    #include "./tool/debug.hpp"
#else
    #define dbg(...)
    #define dbgarr(a)
#endif
using ll = long long;

int main() {
    ios_base::sync_with_stdio(0);
    cin.tie(0);
 
    int n;
    cin >> n;
    vector<int> a(n);
    for(int i = 0; i < n; i++) cin >> a[i];

    // Trie
    vector<vector<int>> nxt = vector<vector<int>>(1, vector<int>(2));
    int id = 0;
    auto insert = [&](int x) -> void {
        int cur = 0;
        for(int i = 31; i >= 0; i--) {
            int bit = (x>>i)&1;
            if(!nxt[cur][bit]) {
                nxt[cur][bit] = ++id;
                nxt.push_back(vector<int>(2));
            }
            cur = nxt[cur][bit];
        }
    };
    auto search = [&](int x) -> int {
        int cur = 0;
        int res = 0;
        for(int i = 31; i >= 0; i--) {
            int bit = (x>>i)&1;
            if(nxt[cur][bit^1]) {
                cur = nxt[cur][bit^1];
                res |= (bit^1)<<i;
            } else {
                cur = nxt[cur][bit];
                res |= bit<<i;
            }
        }
        return res;
    };

    // very like maximum subarray sum
    // in maximum subarray sum we need to find the smallest one to make our sum large
    // but in xor we use trie to help us to find the one that can make our xor large
    insert(0);
    int ans = 0;
    int cur_xor = 0;
    for(int i = 0; i < n; i++) {
        cur_xor ^= a[i];
        int cur_ans = cur_xor^search(cur_xor);
        ans = max(ans, cur_ans);
        insert(cur_xor);
    }
    cout << ans << "\n";
}
```
