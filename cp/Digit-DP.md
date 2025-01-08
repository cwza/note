# Digit DP
* Video Tutorial: https://www.youtube.com/watch?v=heUFId6Qd1A

## Template
* Top-Down DP, tight false says that we can use the digit from 0 to 9, and tight true says that we can only choose digit from 0 to digits[i]
``` cpp
int main() {
    ll a;
    cin >> a;
    vector<int> digits;
    while(a) {
        digits.push_back(a%10);
        a /= 10;
    }
    reverse(digits.begin(), digits.end());
    int n = digits.size();

    vector<vector<ll>> dp(n, vector<ll>(2, -1));
    auto dfs = [&](auto &&self, int pos, int tight) -> ll {
        if(pos==n) return 1;
        if(dp[pos][tight]!=-1) return dp[pos][tight];
        int up = 9;
        if(tight) up = digits[pos];
        ll res = 0;
        for(int i = 0; i <= up; i++) {
            res += self(self, pos+1, tight&(i==up);
        }
        dp[pos][tight] = res;
        return res;
    };
    cout << dfs(dfs, 0, 1) << "\n";
}
```

## Problems
* https://www.spoj.com/problems/PR003004/
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

ll bf(ll a) {
    auto helper2 = [](ll x) -> ll {
        vector<int> digits;
        while(x) {
            digits.push_back(x%10);
            x /= 10;
        }
        reverse(digits.begin(), digits.end());
        ll ret = 0;
        for(int digit : digits) ret += digit;
        return ret;
    };
    ll ans = 0;
    for(ll i = 0; i <= a; i++) {
        ans += helper2(i);
    }
    return ans;
}

ll helper(ll a) {
    if(a<0) return 0;
    vector<int> digits;
    while(a) {
        digits.push_back(a%10);
        a /= 10;
    }
    reverse(digits.begin(), digits.end());
    int n = digits.size();
    vector<vector<pair<ll, ll>>> dp(n, vector<pair<ll, ll>>(2, {-1, -1})); // {cnt, sum}
    auto dfs = [&](auto &&self, int pos, int tight) -> pair<ll, ll> {
        if(pos==n) return {1, 0};
        if(dp[pos][tight]!=make_pair((ll)-1, (ll)-1)) return dp[pos][tight];
        int up = 9;
        if(tight) up = min(up, digits[pos]);
        ll cnt = 0, sum = 0;
        for(int i = 0; i <= up; i++) {
            auto [cur_cnt, cur_sum] = self(self, pos+1, tight&(i==up));
            cnt += cur_cnt;
            sum += cur_cnt*i+cur_sum;
        }
        dp[pos][tight] = {cnt, sum};
        return dp[pos][tight];
    };
    return dfs(dfs, 0, 1).second;
}

void solve() {
    ll a, b;
    cin >> a >> b;
    ll ans_b = helper(b);
    ll ans_a = helper(a-1);
    dbg(ans_b, ans_a);

    cout << ans_b-ans_a << "\n";
}
 
bool multi = 1;
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
* https://cses.fi/problemset/task/2220
* https://atcoder.jp/contests/abc387/tasks/abc387_c
