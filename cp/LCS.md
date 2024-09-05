# Longest Common Subsequence
* https://leetcode.com/problems/longest-common-subsequence/description/
*  `dp[i][j] = LCS length of s1[:i] and s2[:j]`
    + `dp[i][j] = max(dp[i-1][j-1]+1, dp[i][j-1], dp[i-1][j]);`
    + `dp[i][j] = 0, if i=0 or j=0`
``` cpp
#include <bits/stdc++.h>
using namespace std;
#ifdef DEBUG
    #include "debug.hpp"
#else
    #define dbg(...)
#endif
using ll = long long;


void solve() {
    string s1, s2;
    cin >> s1 >> s2;
    int n1 = s1.size();
    int n2 = s2.size();
    s1.insert(s1.begin(), ' ');
    s2.insert(s2.begin(), ' ');

    vector<vector<int>> dp(n1+1, vector<int>(n2+1));
    vector<vector<pair<int, int>>> parent(n1+1, vector<pair<int, int>>(n2+1));
    vector<vector<char>> ans(n1+1, vector<char>(n2+1, ' '));
    for(int i = 1; i <= n1; i++) {
        for(int j = 1; j <= n2; j++) {
            if(s1[i]!=s2[j]) {
                if(dp[i-1][j]>dp[i][j]) {
                    dp[i][j] = dp[i-1][j];
                    parent[i][j] = {i-1, j};
                }
                if(dp[i][j-1]>dp[i][j]) {
                    dp[i][j] = dp[i][j-1];
                    parent[i][j] = {i, j-1};
                }
            } else {
                if(dp[i-1][j-1]+1>dp[i][j]) {
                    dp[i][j] = dp[i-1][j-1]+1;
                    parent[i][j] = {i-1, j-1};
                    ans[i][j] = s1[i];
                }
            }
            // if you don't need to print the LCS sequence then just use follows
            // if(s1[i]==s2[j]) {
            //     dp[i][j] = max(dp[i][j], dp[i-1][j-1]+1);
            // } else {
            //     dp[i][j] = max(dp[i][j], dp[i-1][j]);
            //     dp[i][j] = max(dp[i][j], dp[i][j-1]);
            // }
        }
    }
    cout << dp[n1][n2] << "\n";

    // print the LCS sequence
    int ci = n1, cj = n2;
    string anss;
    while(ci!=0 && cj!=0) {
        if(ans[ci][cj]!=' ') anss += ans[ci][cj]; 
        auto [ni, nj ]= parent[ci][cj];
        ci = ni;
        cj = nj;
    }
    reverse(anss.begin(), anss.end());
    cout << anss << "\n";
}
 
bool multi = 0;
int main() {
    ios_base::sync_with_stdio(0);
    cin.tie(0);
 
    int t = 1;
    if (multi)
        cin >> t;
    for (int i = 0; i < t; i++) {
        solve();
    }
}
```
