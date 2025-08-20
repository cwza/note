# Knapsack Problem


## 0/1 Knapsack, O(n*m)
### Traditional
* Each item you can either take or not take
* Dependency:
    + Pull:     
    ```
    [i-1, j-cost]         [i-1, j]
         |                   |
         |                   v
         ----------------->[i, j]
    ```
    + Push:
    ```
     [i, j]------------------
       |                    |
       v                    v
    [i+1, j]           [i+1, j+cost]
    ```
* Traverse j from large to small: `for(int j=m; j >= 0; j--)`
* https://www.luogu.com.cn/problem/P1048
``` cpp
// Push DP
int main() {
    // Given constrain: cost <= m
    // We want to maximize val
    // dp[i][j]: use only 1...i items, cost<=j, maximized val
    int n, m;
    cin >> m >> n;
    vector<int> cost(n), val(n);
    for(int i = 0; i < n; i++) cin >> cost[i] >> val[i];

    // vector<vector<ll>> dp(n+1, vector<ll>(m+1));
    // for(int i = 0; i < n; i++) {
    //     for(int j = 0; j <= m; j++) {
    //         if(j+cost[i]<=m) {
    //             dp[i+1][j+cost[i]] = dp[i][j];
    //             dp[i+1][j+cost[i]] = max(dp[i+1][j+cost[i]], dp[i][j]+val[i]);
    //         }
    //     }
    // }
    // cout << dp[n][m] << "\n";

    vector<ll> dp(m+1);
    for(int i = 0; i < n; i++) {
        for(int j = m; j >= 0; j--) {
            if(j+cost[i]<=m) dp[j+cost[i]] = max(dp[j+cost[i]], dp[j]+val[i]);
        }
    }
    cout << dp[m] << "\n";
}
```
``` cpp
// Pull DP
int main() {
    int n, m;
    cin >> m >> n;
    vector<int> cost(n+1), val(n+1);
    for(int i = 1; i <= n; i++) cin >> cost[i] >> val[i];

    // vector<vector<ll>> dp(n+1, vector<ll>(m+1));
    // for(int i = 1; i <= n; i++) {
    //     for(int j = 0; j <= m; j++) {
    //         dp[i][j] = dp[i-1][j];
    //         if(j-cost[i]>=0)
    //             dp[i][j] = max(dp[i][j], dp[i-1][j-cost[i]]+val[i]);
    //     }
    // }
    // cout << dp[n][m] << "\n";

    vector<ll> dp(m+1);
    for(int i = 1; i <= n; i++) {
        for(int j = m; j >= 0; j--) {
            if(j-cost[i]>=0) dp[j] = max(dp[j], dp[j-cost[i]]+val[i]);
        }
    }
    cout << dp[m] << "\n";
}
```
### Grouped
* In each group, you can only take one item from group or just take no item from this group (i.e. you can not take 2 or more items from the same group)
* https://www.luogu.com.cn/problem/P1757
``` cpp
int main() {
    int m, n;
    cin >> m >> n;
    map<int, vector<int>> group_items;
    vector<int> cost(n), val(n);
    for(int i = 0, group_id; i < n; i++) {
        cin >> cost[i] >> val[i];
        cin >> group_id;
        group_items[group_id].push_back(i);
    }
    vector<int> dp(m+1);
    for(auto& [_, items] : group_items) {
        for(int j = m; j >= 0; j--) {
            // Take no item from this group
            // dp[j] = dp[j]
            for(int k : items) {
                // Take one item from this group
                if(j-cost[k]>=0) dp[j] = max(dp[j], dp[j-cost[k]]+val[k]);
            }
        }
    }
    cout << dp[m] << "\n";
}
```

## Unbounded Knapsack, O(n*m)
* Each item you can take 1, 2, 3, ..... if you want or just take 0
* Dependency
    + Pull:     
    ```
                          [i-1, j]
                             |
                             v
    [i, j-cost]----------->[i, j]
    ```
    + Push:
    ```
     [i, j]-------------->[i, j+cost]
       |                    
       v                    
    [i+1, j]           
    ```
* Traverse j from small to large: `for(int j = 0; j <= m; j++)`
* https://www.luogu.com.cn/problem/P1616
``` cpp
int main() {
    // Given constrain: cost <= m
    // We want to maximize val
    // dp[i][j]: use only 1...i items, cost<=j, maximized val
    int m, n;
    cin >> m >> n;
    vector<int> cost(n+1), val(n+1);
    for(int i = 1; i <= n; i++) cin >> cost[i] >> val[i];

    // vector<vector<ll>> dp(n+1, vector<ll>(m+1));
    // for(int i = 1; i <= n; i++) {
    //     for(int j = 0; j <= m; j++) {
    //         dp[i][j] = dp[i-1][j];
    //         if(j-cost[i]>=0) dp[i][j] = max(dp[i][j], d[i-1][j-cost[i]]+val[i]);
    //     }
    // }

    vector<ll> dp(m+1);
    for(int i = 1; i <= n; i++) {
        for(int j = 0; j <= m; j++) {
            if(j-cost[i]>=0) dp[j] = max(dp[j], dp[j-cost[i]]+val[i]);
        }
    }
    cout << dp[m] << "\n";
}
```
* https://www.luogu.com.cn/problem/P2918
``` cpp
const int INF = 2e9;
int main() {
    // Given constrain: val >= m
    // We want to minimize cost
    // dp[i][j]: use only 1...i items, val=m, minimized cost
    int n, m;
    cin >> n >> m;
    vector<int> val(n+1), cost(n+1);
    int mx_val = 0;
    for(int i = 1; i <= n; i++) {
        cin >> val[i] >> cost[i];
        mx_val = max(mx_val, val[i]);
    }

    vector<int> dp(m+mx_val+1, INF);
    dp[0] = 0;
    for(int i = 1; i <= n; i++) {
        for(int j = 0; j <= m+mx_val; j++) {
            if(j-val[i]>=0 && dp[j-val[i]]!=INF)
                dp[j] = min(dp[j, dp[j-val[i]]+cost[i]);
        }
    }
    int ans = INF;
    for(int j = m; j <= m+mx_val; j++) ans = min(ans, dp[j]);
    cout << ans << "\n";
}
```

## Bounded Knapsack
* Each item you can only take limited times (ex: for item 1 you can only take 0, 1, 2, 3, item2 you can only take 0, 1, 2)
* https://www.luogu.com.cn/problem/P1776
### No Optimization
* Time Complexity: `O(n*m*max_cnt)`
* Dependency
    + Pull:     
    ```
    [i-1, j-cnt*cost]  ...   [i-1, j-2*cost]       [i-1, j-cost]         [i-1, j]
            |                       |                    |                   |
            v                       v                    v                   v
            -------------------------------------------------------------->[i, j]
    ```
    + Push:
    ```
     [i, j]------------------------------------------------------------
       |                    |                   |                     |
       v                    v                   v                     v
    [i+1, j]           [i+1, j+cost]     [i+1, j+2*cost]  ...  [i+1, j+cnt*cost]
    ```
``` cpp
int main() {
    // This implementation will get TLE
    int n, m;
    cin >> n >> m;
    vector<int> cost(n), val(n), cnt(n);
    for(int i = 0; i < n; i++) cin >> val[i] >> cost[i] >> cnt[i];

    vector<int> dp(m+1);
    for(int i = 0; i < n; i++) {
        for(int j = m; j >= 0; j--) {
            for(int k = 0; k <= cnt[i]; k++) {
                if(j-k*cost[i]>=0)
                    dp[j] = max(dp[j], dp[j-k*cost[i]]+k*val[i]);
            }
        }
    }
    cout << dp[m] << "\n";
}
```
### Binary Lifting Optimization
* Use binary lifting to spilit one item to multiple items and just run 0/1 knapsack on the new items
    + Example: For item which is limited by 50, we can split it to following 6 items
    ```
             cost    val
        A:  1*cost   1*val         1+2+4+8+16+19=50
        B:  2*cost   2*val         {}:0, {A}:1, {B}:2, {A,B}:3, {C}:4, {A,C}:5, {B,C}:6, {A,B,C}:7, {D}:8, {A,D}:9, {B,D}:10
        C:  4*cost   4*val         {A,B,D}:11, {C,D}:12, ....
        D:  8*cost   8*val         You will find that no matter we want to take which number of items, we always can find a subset to match it
        E: 16*cost  16*val
        F: 19*cost  19*val
    ```
* Time Complexity: `O((log(cnt1)+log(cnt2)+...+log(cntn))*m) = O(n*log(max_cnt)*m)`
``` cpp
int main() {
    int n, m;
    cin >> n >> m;
    // split each item by binary lifting
    vector<int> cost, val;
    for(int i = 0; i < n; i++) {
        int cur_cost, cur_val, cur_cnt;
        cin >> cur_val >> cur_cost >> cur_cnt;
        for(int j = 1; j <= cur_cnt; j *= 2) {
            cost.push_back(j*cur_cost);
            val.push_back(j*cur_val);
            cur_cnt -= j;
        }
        if(cur_cnt) {
            cost.push_back(cur_cnt*cur_cost);
            val.push_back(cur_cnt*cur_val);
        }
    }

    // 0/1 knapsack on new items
    n = cost.size();
    vector<int> dp(m+1);
    for(int i = 0; i < n; i++) {
        for(int j = m; j >= 0; j--) {
            if(j-cost[i]>=0)
                dp[j] = max(dp[j], dp[j-cost[i]]+val[i]);
        }
    }
    cout << dp[m] << "\n";
}
```
### Monotonic Deque Optimization
* Group by modulo of cost and use monotonic deque to quickly find sliding window maximum
* Time Complexity: `O(n*m)`
* https://www.bilibili.com/video/BV1Nz4y1c71M/
``` cpp
// This is the solution from https://www.bilibili.com/video/BV1Nz4y1c71M/
int solve2(int n, int m, vector<int> &val, vector<int> &cost, vector<int> &cnt) {
    vector<int> dp(m+1);
    auto get_value = [&](int i, int j) -> int {
        return dp[j]-j/cost[i]*val[i];
    };
    for(int i = 0; i < n; i++) {
        for(int mod = 0; mod < cost[i]; mod++) {
            deque<int> dq;
            for(int j = m-mod; j >= 0; j -= cost[i]) {
                /*
                 * [j-cnt*cost] ... [j-2*cost]  [j-cost]   [j]
                */
                if(j==m-mod) {
                    for(int k = j; k >= 0 && k>=j-cnt[i]*cost[i]; k -= cost[i]) {
                        while(dq.size() && get_value(i, dq.back())<=get_value(i, k)) dq.pop_back();
                        dq.push_back(k);
                    }
                } else {
                    while(dq.size() && dq.front()>j) dq.pop_front();
                    if(j-cnt[i]*cost[i]>=0) {
                        while(dq.size() && get_value(i, dq.back())<= get_value(i, j-cnt[i]*cost[i])) dq.pop_back();
                        dq.push_back(j-cnt[i]*cost[i]);
                    }
                }
                assert(dq.size());
                dp[j] = get_value(i, dq.front())+j/cost[i]*val[i];
            }
        }
    }
    return dp[m];
}

// This is the solution from me
int solve3(int n, int m, vector<int> &val, vector<int> &cost, vector<int> &cnt) {
    vector<int> dp(m+1);
    auto get_value = [&](int i, int j, int right_j) -> int {
        /*                                         dp[j]+k*val
         * dp[right_j-cnt*cost]+cnt*val, ... dp[right_j-k*cost]+k*val, ... dp[right_j-2*cost]+2*val,  dp[right_j-cost]+1*val,   dp[right_j]+0*val
         * right_j-k*cost = j
         * k = (right_j-j)/cost
        */
        return dp[j]+(right_j-j)/cost[i]*val[i];
    };
    for(int i = 0; i < n; i++) {
        for(int mod = 0; mod < cost[i]; mod++) {
            deque<int> dq; // Monotonic Deque to Maintain max_value at front
            for(int j = m-mod; j >= 0; j -= cost[i]) {
                /*
                 * [j-cnt*cost] ... [j-2*cost]  [j-cost]   [j]
                */
                if(j==m-mod) {
                    for(int k = j; k >= 0 && k>=j-cnt[i]*cost[i]; k -= cost[i]) {
                        while(dq.size() && get_value(i, dq.back(), j)<=get_value(i, k, j)) dq.pop_back();
                        dq.push_back(k);
                    }
                } else {
                    while(dq.size() && dq.front()>j) dq.pop_front();
                    if(j-cnt[i]*cost[i]>=0) {
                        while(dq.size() && get_value(i, dq.back(), j)<= get_value(i, j-cnt[i]*cost[i], j)) dq.pop_back();
                        dq.push_back(j-cnt[i]*cost[i]);
                    }
                }
                assert(dq.size());
                dp[j] = get_value(i, dq.front(), j);
            }
        }
    }
    return dp[m];
}

int main() {
    ios_base::sync_with_stdio(0);
    cin.tie(0);

    int n, m;
    cin >> n >> m;
    vector<int> val(n), cost(n), cnt(n);
    for(int i = 0; i < n; i++) cin >> val[i] >> cost[i] >> cnt[i];
    cout << solve3(n, m, val, cost, cnt) << "\n";
}

```
### Combined
* You can use 0/1 knapsack and unbounded knapsack method to speed up bounded knapsack
``` cpp
vector<int> dp(m+1);
for(int i = 0; i < n; i++) {
    if(cnt[i]==1) {
        // 0/1 knapsack
        for (int j = m; j >= cost[i]; j--) {
            if (dp[j - cost[i]]) dp[j] = max(dp[j], dp[j-cost[i]]+val[i])
        }
    } else if (cost[i]*cnt[i]>m) {
        // unbounded knapsack
        for (int j = cost[i]; j <= m; j++) {
            if (dp[j - val[i]]) dp[j] = max(dp[j], dp[j-cost[i]]+val[i]);
        }
    } else {
        // run bounded knapsack ...
    }
}
```

