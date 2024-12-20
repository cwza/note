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
```
sequence : -7 10  9  2  3  8  8  1
temp LIS :
position :

sequence :(-7)10  9  2  3  8  8  1
temp LIS : -7
position :  1		// -7 在 LIS 的第一個位置

sequence : -7(10) 9  2  3  8  8  1
temp LIS : -7 10
position :  1  2	// 10 在 LIS 的第二個位置，以此類推。

sequence : -7 10 (9) 2  3  8  8  1
temp LIS : -7  9
position :  1  2  2
/* 9 成為 LIS 的潛力比 10 大, 所以以 9 代替 10 */

sequence : -7 10  9 (2) 3  8  8  1
temp LIS : -7  2
position :  1  2  2  2
/* 2 成為 LIS 的潛力比 9 大, 所以以 2 代替 9 */

sequence : -7 10  9  2 (3) 8  8  1
temp LIS : -7  2  3
position :  1  2  2  2  3

sequence : -7 10  9  2  3 (8) 8  1
temp LIS : -7  2  3  8
position :  1  2  2  2  3  4

sequence : -7 10  9  2  3  8 (8) 1
temp LIS : -7  2  3  8
position :  1  2  2  2  3  4  4
/* 8 成為 LIS 的潛力比 8 大, 所以以 8 代替 8 */

sequence : -7 10  9  2  3  8  8 (1)
temp LIS : -7  1  3  8
position :  1  2  2  2  3  4  4  2
/* 1 成為 LIS 的潛力比 2 大, 所以以 1 代替 2 */

-------------------

sequence : -7 10  9  2  3  8 (8) 1
position :  1  2  2  2  3  4 (4) 2
LIS      :  -  -  -  8
/* search 4th, 8 is fourth LIS element */

sequence : -7 10  9  2 (3) 8  8  1
position :  1  2  2  2 (3) 4  4  2
LIS      :  -  -  3  8
/* search 3rd, 3 is third LIS element */

sequence : -7 10  9 (2) 3  8  8  1
position :  1  2  2 (2) 3  4  4  2
LIS      :  -  2  3  8
/* search 2nd, 2 is second LIS element */

sequence :(-7)10  9  2  3  8  8  1
position : (1) 2  2  2  3  4  4  2
LIS      : -7  2  3  8
/* search 1st, -7 is first LIS element */
```
```cpp
int main() {
    int n;
    cin >> n;
    vector<int> a(n);
    for(int i = 0; i < n; i++) cin >> a[i];

    /*
         0 1 2 3 4 5 6 7
    a:   7 3 5 3 6 2 9 8
    arr: 2 5 6 8
    pos: 0 0 1 0 2 0 3 3
    */
    vector<int> arr;
    vector<int> pos(n);
    for(int i = 0; i < n; i++) {
        int id = lower_bound(arr.begin(), arr.end(), a[i])-arr.begin();
        if(id==(int)arr.size()) {
            arr.push_back(a[i]);
        } else {
            arr[id] = a[i];
        }
        pos[i] = id;
    }

    // print the LIS length
    int len = arr.size();
    cout << len << "\n";

    // print the LIS sequence
    vector<int> ans;
    for(int i = n-1; i >= 0; i--) {
        if(pos[i]==len-1) {
            ans.push_back(a[i]);
            len--;
        }
    }
    reverse(ans.begin(), ans.end());
    for(int i = 0; i < (int)ans.size(); i++) cout << ans[i] << " ";
    cout << "\n";
}
```

## LDS or LNIS
- To find the longest decreasing subsequence, just reverse the origin array and find the LIS
- To find the longest non-increasing subsequence, just reverse the origin array and find the LNDS(longest non-decreasing subsequence)
