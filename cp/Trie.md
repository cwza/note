# Trie
* https://www.youtube.com/watch?v=MyiHeqtwOWQ
## Problem1
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

## Problem2
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
