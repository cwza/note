# Sliding Window
* Can be used on many sub-string and sub-array problems
* The range of sub-string and the answer need to have some monotonic relation
* Two Method
    + Calculate each answer for each l
    + Calculate each answer for each r

## Minimum Size Subarray Sum
* https://leetcode.com/problems/minimum-size-subarray-sum/description/
* Find minimum size: calculate answer in while
    + Think of this [T, T, T, T, T, T, F, F, F, F, F], you want to find the first False
``` cpp
class Solution {
public:
    // calculate each answer on each r
    int minSubArrayLen(int target, vector<int>& nums) {
        int n = nums.size();
        int l = 0;
        int cur = 0;
        int ans = n+1;
        for(int r = 0; r < n; r++) {
            cur += nums[r];
            while(cur>=target) {
                ans = min(ans, r-l+1);
                cur -= nums[l];
                l++;
            }
        }
        if(ans>n) return 0;
        return ans;
    }
    // // calculate each answer on each l
    // int minSubArrayLen(int target, vector<int>& nums) {
    //     int n = nums.size();
    //     int r = 0;
    //     int cur = 0;
    //     int ans = n+1;
    //     for(int l = 0; l < n; l++) {
    //         while(r<n && cur<target) {
    //             cur += nums[r];
    //             r++;
    //         }
    //         if(cur>=target) ans = min(ans, r-l);
    //         cur -= nums[l];
    //     }
    //     if(ans>n) return 0;
    //     return ans;
    // }
};
```

## Longest Substring Without Repeating Characters
* https://leetcode.com/problems/longest-substring-without-repeating-characters/
* Find longest size: calculate answer out of while
    + Think of this [F, F, F, F, F, F, T, T, T, T, T], you want to find the first True
``` cpp
class Solution {
public:
    // calculate each answer for each r
    int lengthOfLongestSubstring(string s) {
        int n = s.size();
        if(!n) return 0;

        vector<int> st(256);
        int l = 0;
        int ans = 0;
        for(int r = 0; r < n; r++) {
            while(st[s[r]]) {
                st[s[l]] = 0;
                l++;
            }
            ans = max(ans, r-l+1);
            st[s[r]] = 1;
        }
        return ans;
    }
    // // calculate each answer for each l
    // int lengthOfLongestSubstring(string s) {
    //     int n = s.size();
    //     if(!n) return 0;

    //     vector<int> st(256);
    //     int r = 0;
    //     int ans = 0;
    //     for(int l = 0; l < n; l++) {
    //         while(r<n && !st[s[r]]) {
    //             st[s[r]] = 1;
    //             r++;
    //         }
    //         ans = max(ans, r-l);
    //         st[s[l]] = 0;
    //     }
    //     return ans;
    // }
};
```
