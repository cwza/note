# Digit DP
* Video Tutorial: https://www.youtube.com/watch?v=heUFId6Qd1A

## Template
* Free true says that we can use the digit from 0 to 9, and free false says that we can only choose digit from 0 to digits[pos]
* Once free becomes true, it will be true for all positions after.
### Basic Template
* Top-Down DP
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
    auto dfs = [&](auto &&self, int pos, int free) -> ll {
        if(pos==n) return 1;
        if(dp[pos][free]!=-1) return dp[pos][free];
        int up = 9;
        if(!free) up = digits[pos];
        ll res = 0;
        for(int i = 0; i <= up; i++) {
            res += self(self, pos+1, free|(i!=up));
        }
        dp[pos][free] = res;
        return res;
    };
    cout << dfs(dfs, 0, 0) << "\n";
}
```
* Bottom-Up DP
``` cpp
// Bottom-Up DP
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

    vector<vector<ll>> dp(n+1, vector<ll>(2));
    dp[n][0] = 1;
    dp[n][1] = 1;
    for(int pos = n-1; pos >= 0; pos--) {
        for(int free = 0; free < 2; free++) {
            int up = 9;
            if(!free) up = digits[pos];
            for(int i = 0; i <= up; i++) {
                dp[pos][free] += dp[pos+1][free|(i!=up)];
            }
        }
    }
    cout << dp[0][0] << "\n";
}
```
### Handle prefix 0
* Prefix true says that currently the prefix are all 0 (ex: 000000i), false says that currently the prefix are not all 0 (ex: 00004507i)
* This variable is very useful when you need to handle prefix 0 case
    + ex: count non-duplicated numbers (ex: 1345 ok, 1233 not ok, but 0045 is also ok)
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

    vector<vector<vector<ll>>> dp(n, vector<vector<ll>>(2, vector<ll>(2, -1)));
    auto dfs = [&](auto &&self, int pos, int free, int prefix) -> ll {
        if(pos==n) return 1;
        if(dp[pos][free][prefix]!=-1) return dp[pos][free][prefix];
        int up = 9;
        if(!free) up = digits[pos];
        ll res = 0;
        for(int i = 0; i <= up; i++) {
            // handle prefix==true case here
            res += self(self, pos+1, free|(i!=up), prefix&(i==0));
        }
        dp[pos][free][prefix] = res;
        return res;
    };
    cout << dfs(dfs, 0, 0, 1) << "\n";
}
```
* If you need to track the last number pre, then you can use it to track prefix (use 10 to represent prefix all 0)
    + pre = 5 says that previous number is 5, pre = 10 says that currently the prefix are all 0 (ex: 000000i)
``` cpp
    auto dfs = [&](auto &&self, int pos, int free, int pre) -> ll {
        if(pos==n) return 1;
        if(dp[pos][free][pre]!=-1) return dp[pos][free][pre];
        int up = 9;
        if(!free) up = digits[pos];
        ll res = 0;
        for(int i = 0; i <= up; i++) {
            int cur = i;
            if(i==0&&pre==10) cur = 10;
            // handle pre==10 case here
            res += self(self, pos+1, free|(i!=up), cur);
        }
        dp[pos][free][pre] = res;
        return res;
    };
    dfs(dfs, 0, 0, 10);
```

## Count on Range
* Count from a to b
    + Answer = Answer(0 to b)-Answer(0 to a-1)
* Usually a is very big (ex: 10^100), how to calculate a-1 ???
    + Method1: Calculate a-1 directly on the array (following code shows this)
    + Method2: Answer = Answer(0 to b)-Answer(0 to a) + Answer(a)
``` cpp
int main {
    string a, b;
    cin >> a >> b;
    vector<int> digits1, digits2;
    for(char ch : a) digits1.push_back(ch-'0');
    for(char ch : b) digits2.push_back(ch-'0');
    // calculate a-1 on vector<int> digits1
    for(int i = (int)digits1.size()-1; i >= 0; i--) {
        if(digits1[i]==0) digits1[i] = 9;
        else {
            digits1[i]--;
            break;
        }
    }
    auto dfs = [&](auto &&self, int pos, int free, vector<int> &digits, vector<vector<ll>> &dp) -> ll {
        // ... skip
    };
    ll ans1 = dfs(dfs, 0, 0, digits1, dp1);
    ll ans2 = dfs(dfs, 0, 0, digits2, dp2);
    cout << ans2-ans1 << "\n";
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
    auto dfs = [&](auto &&self, int pos, int free) -> pair<ll, ll> {
        if(pos==n) return {1, 0};
        if(dp[pos][free]!=make_pair((ll)-1, (ll)-1)) return dp[pos][free];
        int up = 9;
        if(!free) up = min(up, digits[pos]);
        ll cnt = 0, sum = 0;
        for(int i = 0; i <= up; i++) {
            auto [cur_cnt, cur_sum] = self(self, pos+1, free|(i!=up));
            cnt += cur_cnt;
            sum += cur_cnt*i+cur_sum;
        }
        dp[pos][free] = {cnt, sum};
        return dp[pos][free];
    };
    return dfs(dfs, 0, 0).second;
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
