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
- https://www.bilibili.com/video/BV1ne411D7CQ
- end[i]: 目前所有長度為i+1的遞增子序列的最小結尾 
    + This end array is guaranteed to be strictly increasing
- len[i]: max LIS length that end with a[i]
```
a   : -7 10  9  2  3  8  8  1
end :
len :

a   :(-7)10  9  2  3  8  8  1
end : (-7)
len :  1		            
// 長度為1的遞增子序列中end最小的是-7

a   : -7(10) 9  2  3  8  8  1
end : -7 10
len :  1  2	    
// 長度為2的遞增子序列中end最小的是10 
// lower_bound(10) 找不到東西, 表示目前所有在end裡的值皆小於10, 所以他的長度應該是目前end的長度+1, 也就是2

a   : -7 10 (9) 2  3  8  8  1
end : -7  9
len :  1  2  2  
// 長度為2的遞增子序列中end最小的是9
// 9 成為 LIS 的潛力比 10 大, 所以以 9 代替 10
// lower_bound(9)會找到原本10的那個位置, 表示目前在end裡有1個值嚴格小於9, 所以他的長度應該是2

a   : -7 10  9 (2) 3  8  8  1
end : -7  2
len :  1  2  2  2
// 長度為2的遞增子序列中end最小的是2
// 2 成為 LIS 的潛力比 9 大, 所以以 2 代替 9
// lower_bound(2)會找到原本9的那個位置, 表示目前在end裡有1個值嚴格小於2, 所以他的長度應該是2

a   : -7 10  9  2 (3) 8  8  1
end : -7  2  3
len :  1  2  2  2  3
// 長度為3的遞增子序列中end最小的是3
// lower_bound(3) 找不到東西, 表示目前所有在end裡的值皆小於3, 所以他的長度應該是目前end的長度+1, 也就是3

a   : -7 10  9  2  3 (8) 8  1
end : -7  2  3  8
len :  1  2  2  2  3  4
// 長度為4的遞增子序列中end最小的是8
// lower_bound(8) 找不到東西, 表示目前所有在end裡的值皆小於8, 所以他的長度應該是目前end的長度+1, 也就是4

a   : -7 10  9  2  3  8 (8) 1
end : -7  2  3  8
len :  1  2  2  2  3  4  4
// 長度為4的遞增子序列中end最小的是8
// 8 成為 LIS 的潛力比 8 大, 所以以 8 代替 8
// lower_bound(8)會找到原本8的那個位置, 表示目前在end裡有3個值嚴格小於8, 所以他的長度應該是4

a   : -7 10  9  2  3  8  8 (1)
end : -7  1  3  8
len :  1  2  2  2  3  4  4  2
// 長度為2的遞增子序列中end最小的是1
// 1 成為 LIS 的潛力比 2 大, 所以以 1 代替 2
// lower_bound(1)會找到原本2的那個位置, 表示目前在end裡有1個值嚴格小於2, 所以他的長度應該是2

-------------------
From right to left:

a   : -7 10  9  2  3  8 (8) 1
len :  1  2  2  2  3  4 (4) 2
LIS      :  -  -  -  8
/* search 4th, 8 is fourth LIS element */

a   : -7 10  9  2 (3) 8  8  1
len :  1  2  2  2 (3) 4  4  2
LIS      :  -  -  3  8
/* search 3rd, 3 is third LIS element */

a   : -7 10  9 (2) 3  8  8  1
len :  1  2  2 (2) 3  4  4  2
LIS      :  -  2  3  8
/* search 2nd, 2 is second LIS element */

a   :(-7)10  9  2  3  8  8  1
len : (1) 2  2  2  3  4  4  2
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
    end: 2 5 6 8
    len: 1 1 2 1 3 1 4 4
    LIS: 3 5 6 8
    */
    vector<int> end;
    vector<int> len(n);
    for(int i = 0; i < n; i++) {
        int j = lower_bound(end.begin(), end.end(), a[i])-end.begin();
        if(j==(int)end.size()) {
            end.push_back(a[i]);
        } else {
            end[j] = a[i];
        }
        len[i] = j+1;
    }

    // print the LIS length
    cout << end.size() << "\n";

    // print the LIS sequence
    int cur = end.size();
    vector<int> ans;
    for(int i = n-1; i >= 0; i--) {
        if(len[i]==cur) {
            ans.push_back(a[i]);
            cur--;
        }
    }
    reverse(ans.begin(), ans.end());
    for(int i = 0; i < (int)ans.size(); i++) cout << ans[i] << " ";
    cout << "\n";

}
```

## LNDS or LDS or LNIS
- To find the longest non-decreasing subsequence, just use upper_bound to replace the lower_bound in LIS
- To find the longest decreasing subsequence, use lower_bound(..., greater<int>())
- To find the longest non-increasing subsequence, use upper_bound(..., greater<int>())
