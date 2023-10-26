# Travel Salesman Problem by Bitmask DP
- [E - Traveling Salesman among Aerial Cities (atcoder.jp)](https://atcoder.jp/contests/abc180/tasks/abc180_e)

## Pull DP
```cpp
#include <bits/stdc++.h>
using namespace std;
#define ll long long
#define ar array

/*
https://www.youtube.com/watch?v=hh-uFQ-MGfw
from start to i through nodes in S (S includes i)
dp[S][i] = min([dp[S\{i}][j]+cost[j][i] for j in S and j->i have edge])
 
Time: O(2^n * n^2), Space: O(2^n * n)
Note that the number of edges is much larger than the number of nodes, so we use adjacency matrix instead of adjacency list
*/

const ll inf = 1e18+10;

int main() {
    ios::sync_with_stdio(0); 
    cin.tie(0);
    // freopen("input.txt", "r", stdin); 
    // freopen("output.txt", "w", stdout);

    int n;
    cin >> n;
    vector<tuple<int, int, int>> inp(n);
    for(int i = 0, x, y ,z; i < n; i++) {
        cin >> x >> y >> z;
        inp[i] = {x, y, z};
    }
    vector<vector<ll>> cost(n, vector<ll>(n));
    for(int i = 0; i < n; i++) {
        for(int j = 0; j < n; j++) {
            auto [a, b, c] = inp[i];
            auto [p, q, r] = inp[j];
            cost[i][j] = abs(p-a)+abs(q-b)+max(0, r-c);
        }
    }

    vector<vector<ll>> dp(1<<n, vector<ll>(n, inf));
    dp[0][0] = 0;
    for(int S = 1; S < 1<<n; S++) {
        for(int i = 0; i < n; i++) {
            if(!(S&(1<<i))) continue;
            for(int j = 0; j < n; j++) {
                dp[S][i] = min(dp[S][i], dp[S^(1<<i)][j]+cost[j][i]);
            }
        }
    }

    // for(int S = 0; S < 1<<n; S++) {
    //     for(int i = 0; i < n; i++) cout << dp[S][i] << ",";
    //     cout << endl;
    // }
    cout << dp[(1<<n)-1][0];
}
```

## Push DP
```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;

void IO(string name = "") {
	cin.tie(0)->sync_with_stdio(0);
	if (name.size()) {
		freopen((name + ".in").c_str(), "r", stdin);
		freopen((name + ".out").c_str(), "w", stdout);
	}
}

const ll inf = 1e18+10;

int main() {
    // IO("pcb");

    int n;
    cin >> n;
    vector<tuple<int, int, int>> inp(n);
    for(int i = 0, x, y ,z; i < n; i++) {
        cin >> x >> y >> z;
        inp[i] = {x, y, z};
    }
    vector<vector<ll>> cost(n, vector<ll>(n));
    for(int i = 0; i < n; i++) {
        for(int j = 0; j < n; j++) {
            auto [a, b, c] = inp[i];
            auto [p, q, r] = inp[j];
            cost[i][j] = abs(p-a)+abs(q-b)+max(0, r-c);
        }
    }

    vector<vector<ll>> dp(1<<n, vector<ll>(n, inf));
    dp[1][0] = 0;

    for(int i = 0; i < 1<<n; i++) {
        for(int j = 0; j < n; j++) {
            if(!(i&(1<<j))) continue;
            for(int k = 0; k < n; k++) {
                if(i&(1<<k)) continue;
                if(dp[i][j]==inf) continue;
                dp[i^(1<<k)][k] = min(dp[i^(1<<k)][k], dp[i][j]+cost[j][k]);
            }
        }
    }
    // for(int i = 0; i < 1<<n; i++) {
    //     for(int j = 0; j < n; j++) cout << dp[i][j] << " ";
    //     cout << endl;
    // }

    ll ans = inf;
    for(int j = 0; j < n; j++) {
        if(dp[(1<<n)-1][j]==inf) continue;
        ans = min(ans, dp[(1<<n)-1][j]+cost[j][0]);
    }
    cout << ans;
}
```
