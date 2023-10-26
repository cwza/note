# Range DP

## 2 kinds of loop
- loop from smaller length to larger lengt
```cpp
// [l, r], base case is [i, i], length 1 string
for(int l = n-1; l >= 0; l--) {
		for(int r = 0; r < n; r++) {
		}
}

for(int len = 1; len <= n; len++) {
		for(int l = 0; l <= n-l; l++) {
				int r = l+len-1;
		}
}

// [l, r), base case is [i, i], empty string
for(int l = n-1; l >= 0; l--) {
    for(int r = l+1; r <= n; r++) {
    }
}

for(int len = 1; len <= n; len++) {
    for(int l = 0; l <= n-len; l++) {
        int r = l+len;
    }
}
```

## Practice
* [Minimum Cost to Cut a Stick - LeetCode](https://leetcode.com/problems/minimum-cost-to-cut-a-stick/)
* [Burst Balloons - LeetCode](https://leetcode.com/problems/burst-balloons/)
