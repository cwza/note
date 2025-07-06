# Trie (Prefix Tree)
* https://www.youtube.com/watch?v=MyiHeqtwOWQ
* https://www.bilibili.com/video/BV1Yu4y1Q7vR
## Problem 1
* https://www.nowcoder.com/practice/7f8a8553ddbf4eaab749ec988726702b
* If the size of characters set is large then use `<vector<map<int, int>>>` as nxt and use `!nxt[cur].count(ch_num)` to check existed
``` cpp
#include <bits/stdc++.h>
using namespace std;

class Trie {
public:
    vector<vector<int>> nxt;
    vector<int> end; // number of strings end of this char
    vector<int> pass; // number of strings which prefix through this char
    int id = 1;
    Trie() {
        nxt = vector<vector<int>>(2, vector<int>(26));
        end = vector<int>(2);
        pass = vector<int>(2);
    }
    
    void insert(string word) {
        int cur = 1;
        pass[cur]++;
        for(char ch : word) {
            int ch_num = ch-'a';
            if(!nxt[cur][ch_num]) {
                nxt[cur][ch_num] = ++id;
                nxt.push_back(vector<int>(26));
                end.push_back(0);
                pass.push_back(0);
            }
            cur = nxt[cur][ch_num];
            pass[cur]++;
        }
        end[cur]++;
    }
    
    // returns number of word in trie
    int search(string word) {
        int cur = 1;
        for(char ch : word) {
            int ch_num = ch-'a';
            if(!nxt[cur][ch_num]) return 0;
            cur = nxt[cur][ch_num];
        }
        return end[cur];
    }

    // returns number of word that has this prefix in trie
    int prefix_number(string pre) {
        int cur = 1;
        for(char ch : pre) {
            int ch_num = ch-'a';
            if(!nxt[cur][ch_num]) return 0;
            cur = nxt[cur][ch_num];
        }
        return pass[cur];
    }

    void del(string word) {
        if(!search(word)) return;
        int cur = 1;
        for(char ch : word) {
            int ch_num = ch-'a';
            if(--pass[nxt[cur][ch_num]]==0) {
                nxt[cur][ch_num] = 0;
                return;
            }
            cur = nxt[cur][ch_num];
        }
        end[cur]--;
    }
};


int main() {
    int m;
    cin >> m;
    auto trie = Trie();
    while(m--) {
        int op;
        string word;
        cin >> op >> word;
        if(op==1) {
            trie.insert(word);
        } else if(op==2) {
            trie.del(word);
        } else if(op==3) {
            if(trie.search(word)) cout << "YES\n";
            else cout << "NO\n";
        } else cout << trie.prefix_number(word) << "\n";
    }
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
        vector<vector<int>> nxt = vector<vector<int>>(2, vector<int>(26));
        vector<int> end = vector<int>(2);
        int id = 1;
        auto insert = [&](string &s) -> void {
            int cur = 1;
            for(char ch : s) {
                int ch_num = ch-'a';
                if(!nxt[cur][ch_num]) {
                    nxt[cur][ch_num] = ++id;
                    nxt.push_back(vector<int>(26));
                    end.push_back(0);
                }
                cur = nxt[cur][ch_num];
            }
            end[cur]++;
        };
        for(string &s : words) insert(s);

        string ans = "";
        auto dfs = [&](auto &self, int u, string &s) ->void {
            if(s.size()>ans.size()) ans = s;
            for(int i = 0; i < 26; i++) {
                int v = nxt[u][i];
                if(v && end[v]) {
                    s += char(i+'a');
                    self(self, v, s);
                    s.pop_back();
                }
            }
        };
        string s = "";
        dfs(dfs, 1, s);
        return ans;
    }
};
```

## Problem 3 - Maximum Xor Subarray
* https://cses.fi/problemset/task/1655/
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
    vector<vector<int>> nxt = vector<vector<int>>(2, vector<int>(2));
    int id = 1;
    auto insert = [&](int x) -> void {
        int cur = 1;
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
        int cur = 1;
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
