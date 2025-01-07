# Digit DP
* Video Tutorial: https://www.youtube.com/watch?v=heUFId6Qd1A

## Template
* Top-Down DP, tight false says that we can use the digit from 0 to 9, and tight true says that we can only choose digit from 0 to digits[i]
``` cpp
ll a;
cin >> a
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
```

## Problems
* https://cses.fi/problemset/task/2220
``` cpp
const int maxN = 20;
ll dp[maxN][2][11][2];

ll helper(ll a) {
    if(a==-1) return 0;
    vector<int> digits;
    while(a) {
        digits.push_back(a%10);
        a /= 10;
    }
    reverse(digits.begin(), digits.end());
    // dbg(digits);
    int n = digits.size();
    for(int i = 0; i < n; i++) {
        for(int j = 0; j < 2; j++) {
            for(int k = 0; k < 11; k++) {
                for(int l = 0; l < 2; l++) dp[i][j][k][l] = -1;
            }
        }
    }
    auto dfs = [&](auto &&self, int pos, int tight, int prev, int flag) -> ll {
        if(pos==n) return 1;
        if(dp[pos][tight][prev][flag]!=-1) return dp[pos][tight][prev][flag];
        int up = 9;
        if(tight) up = digits[pos];
        ll res = 0;
        for(int i = 0; i <= up; i++) {
            if((!flag&&i==0) || i!=prev) {
                res += self(self, pos+1, tight&(i==up), i, flag|(i!=0));
            }
        }
        dp[pos][tight][prev][flag] = res;
        return res;
    };
    return dfs(dfs, 0, 1, 10, 0);
}

void solve() {
    ll a, b;
    cin >> a >> b;
    ll ans_b = helper(b);
    ll ans_a = helper(a-1);
    dbg(ans_b, ans_a);

    cout << ans_b-ans_a << "\n";
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
``` cpp
const int maxN = 20;
ll dp[maxN][2][11][2];

ll helper(ll a) {
    if(a==-1) return 0;
    vector<int> digits;
    while(a) {
        digits.push_back(a%10);
        a /= 10;
    }
    reverse(digits.begin(), digits.end());
    // dbg(digits);
    int n = digits.size();
    for(int i = 0; i < n; i++) {
        for(int j = 0; j < 2; j++) {
            for(int k = 0; k < 11; k++) {
                for(int l = 0; l < 2; l++) dp[i][j][k][l] = -1;
            }
        }
    }
    auto dfs = [&](auto &&self, int pos, int tight, int prev, int flag) -> ll {
        if(pos==n) return 1;
        if(dp[pos][tight][prev][flag]!=-1) return dp[pos][tight][prev][flag];
        int up = 9;
        if(tight) up = digits[pos];
        ll res = 0;
        for(int i = 0; i <= up; i++) {
            if((!flag&&i==0) || i!=prev) {
                res += self(self, pos+1, tight&(i==up), i, flag|(i!=0));
            }
        }
        dp[pos][tight][prev][flag] = res;
        return res;
    };
    return dfs(dfs, 0, 1, 10, 0);
}

void solve() {
    ll a, b;
    cin >> a >> b;
    ll ans_b = helper(b);
    ll ans_a = helper(a-1);
    dbg(ans_b, ans_a);

    cout << ans_b-ans_a << "\n";
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
