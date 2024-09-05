# Range DP

## 1D
### 2 kinds of loop
- O(n^2)
- loop from smaller length to larger length
```cpp
// [l, r]
// remember to initialize base case dp[i][i], length 1 string case
for(int l = n-1; l >= 0; l--) {
    for(int r = l; r < n; r++) {
    }
}

for(int len = 1; len <= n; len++) {
    for(int l = 0; l+len-1 < n; l++) {
        int r = l+len-1;
    }
}

// [l, r)
// remember to initialize base case dp[i][i], empty string case
for(int l = n-1; l >= 0; l--) {
    for(int r = l+1; r <= n; r++) {
    }
}

for(int len = 1; len <= n; len++) {
    for(int l = 0; l+len <= n; l++) {
        int r = l+len;
    }
}
```

### Practice
* [Minimum Cost to Cut a Stick - LeetCode](https://leetcode.com/problems/minimum-cost-to-cut-a-stick/)
* [Burst Balloons - LeetCode](https://leetcode.com/problems/burst-balloons/)


## 2D
### 2 kinds of loop
- O(H^2 * W^2)
- loop from inner to outer
```cpp
// [x1, x2][y1, y2]
// remember to initialize base case dp[i][i][i][i], 1*1 grid
for(int x1 = H-1; x1 >= 0; x1--) {
    for(int x2 = x1; x2 < H; x2++) {
        for(int y1 = W-1; y1 >= 0; y1--) {
            for(int y2 = y1; y2 < W; y2++) {
            }
        }
    }
}

for(int len1 = 1; len1 <= H; len1++) {
    for(int x1 = 0; x1+len1-1 < H; x1++) {
        int x2 = x1+len1-1;
        for(int len2 = 1; len2 <= W; len2++) {
            for(int y1 = 0; y1+len2-1 < W; jy++) {
                int y2 = y1+len2-1;
            }
        }
    }
}

// [x1, x2)[y1, y2)
// remember to initialize base case dp[i][i][i][i], empty grid
for(int x1 = H-1; x1 >= 0; x1--) {
    for(int x2 = x1+1; x2 <= H; x2++) {
        for(int y1 = W-1; y1 >= 0; y1--) {
            for(int y2 = y1+1; y2 <= W; y2++) {
            }
        }
    }
}

for(int len1 = 1; len1 <= H; len1++) {
    for(int x1 = 0; x1+len1 <= H; x1++) {
        int x2 = x1+len1;
        for(int len2 = 1; len2 <= W; len2++) {
            for(int y1 = 0; y1+len2 <= W; y1++) {
                int y2 = y1+len2;
            }
        }
    }
}
```

### Practice
* https://codeforces.com/problemset/problem/1198/D
