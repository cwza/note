[[toc]]

# LIS
[CSES - Increasing Subsequence](https://cses.fi/problemset/task/1145)

## Solve by DP, O(n^2)
- This will got TLE on CSES, but it is a good start point
- [Longest Increasing Subsequence - LeetCode](https://leetcode.com/problems/longest-increasing-subsequence/)
```cpp
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int n = nums.size();
        vector<int> dp(n);
        for(int i = 0; i < n; i++) {
            dp[i] = 1;
            for(int j = 0; j < i; j++) {
                if(nums[j]<nums[i]) dp[i] = max(dp[i], dp[j]+1);
            }
        }
        
        int ans = 0;
        for(int a : dp) ans = max(ans, a);
        return ans;
    }
};
```

## Solve by DP + Segment Tree(Range Max Query)
- The key of our segment tree is the value of the origin array. (Because we only want to query the one which is smaller than current), the value in the segment tree is just the dp value same as above.
- We must use coordinate compress to compress the original array value from maxX(1e9) → maxN(1e6), to that we can store it in our segment tree(array)
```cpp
class MaxSegTree {
    private:
        int len;
        vector<int> segtree;
    public:
        MaxSegTree(int len) : len(len), segtree(2 * len) {}
        void set(int ind, int val) {
            for (segtree[ind += len] = val; ind > 1; ind >>= 1) {
                segtree[ind >> 1] = max(segtree[ind], segtree[ind ^ 1]);
            }
        }
 
	// maximum of the range [from, to)
	int range_max(int from, int to) {
		int max_ = INT32_MIN;
		for (from += len, to += len; from < to; from >>= 1, to >>= 1) {
			if ((from & 1) != 0) { max_ = max(max_, segtree[from++]); }
			if ((to & 1) != 0) { max_ = max(max_, segtree[--to]); }
		}
		return max_;
	}
};
 
const int maxX = 1e6;
 
map<int,int> get_compress_map(vector<int> inp) {
    sort(inp.begin(), inp.end());
    map<int, int> mp;
    for(int i = 0; i < inp.size(); i++) mp[inp[i]] = i;
    return mp;
}
 
int main() {
    // IO("cbarn2");
 
    int N;
    cin >> N;
    vector<int> inp(N);
    for(int i = 0; i < N; i++) cin >> inp[i];
    auto compress_map = get_compress_map(inp);
 
    auto segtree = MaxSegTree(maxX);
    int ans = 0;
    for(int i = 0; i < N; i++) {
        int compressed_val = compress_map[inp[i]];
        int prev_max = segtree.range_max(0, compressed_val);
        if(prev_max==INT32_MIN) {
            segtree.set(compressed_val, 1);
            ans = max(ans, 1);
        } else {
            segtree.set(compressed_val, prev_max+1);
            ans = max(ans, prev_max+1);
        }
    }
    cout << ans;
}
```

## Solve by Binary Search, O(nlogn)
- If you want to get the length of the longest non-decreasing subsequence, then use upper_bound instead of lower_bound
```cpp
int main() {
    // IO("cbarn2");
 
    int N;
    cin >> N;
    
    vector<int> ar;
    while(N--) {
        int a;
        cin >> a;
        auto iter = lower_bound(ar.begin(), ar.end(), a);
        if(iter==ar.end()) ar.push_back(a);
        else *iter = a;
    }
    cout << ar.size();
}
```

## LDS or LNIS
- To find the longest non-increasing subsequence, just reverse the origin array and find the LIS
- To find the longest decreasing subsequence, just reverse the origin array and find the LNDS(longest non-decreasing subsequence)
