# Monotonic Stack and Monotonic Deque


## Monotonic Stack
* https://www.bilibili.com/video/BV1HH4y1X7T9

### For each array value, find the first left element and the first right element that less than it
* https://www.nowcoder.com/practice/2a2c00e7a88a498693568cef63a4b7bb
* Pop out the value that larger or equal to me from stack top, to maintain a monotonic increasing stack
* The index on the top of stack is the answer for current index
``` cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    ios_base::sync_with_stdio(0);
    cin.tie(0);
 
    int n;
    cin >> n;
    vector<int> a(n);
    for(int i = 0; i < n; i++) cin >> a[i];

    vector<int> st;
    vector<int> left(n), right(n);
    for(int i = 0;  i < n; i++) {
        while(st.size() && a[st.back()]>=a[i]) st.pop_back();
        if(st.size()) left[i] = st.back();
        else left[i] = -1;
        st.push_back(i);
    }
    st.clear();
    for(int i = n-1; i >= 0; i--) {
        while(st.size() && a[st.back()]>=a[i]) st.pop_back();
        if(st.size()) right[i] = st.back();
        else right[i] = -1;
        st.push_back(i);
    }
    for(int i = 0; i < n; i++) {
        cout << left[i] << " " << right[i] << "\n";
    }
}
```

### Sum of Subarray Minimums
* https://leetcode.com/problems/sum-of-subarray-minimums/
* Contribution Technique: For each element, count how many subarrays include it
* Monotonic Stack: first left and first right element less than current
* Notice the >= for left and > for right, use this trick we can avoid duplicated count
``` cpp
using ll = long long;
const ll MOD = 1e9+7;
class Solution {
public:
    int sumSubarrayMins(vector<int>& arr) {
        int n = arr.size();
        vector<int> left(n), right(n);
        vector<int> st;
        for(int i = 0; i < n; i++) {
            while(st.size() && arr[st.back()]>=arr[i]) st.pop_back();
            if(st.size()) left[i] = st.back();
            else left[i] = -1;
            st.push_back(i);
        }
        st.clear();
        for(int i = n-1; i >= 0; i--) {
            while(st.size() && arr[st.back()]>arr[i]) st.pop_back();
            if(st.size()) right[i] = st.back();
            else right[i] = n;
            st.push_back(i);
        }
        ll ans = 0;
        for(int i = 0; i < n; i++) {
            ans += (ll)arr[i]*(i-left[i])%MOD*(right[i]-i)%MOD;
            ans %= MOD;
        }
        return ans;
    }
};
```

## Monotonic Deque
* https://www.bilibili.com/video/BV11h4y1w7Bu/

### Sliding Window Maximum
* https://leetcode.com/problems/sliding-window-maximum/description/
* Expanding window (r++): Pop out the value that smaller or equal to me from back of deque to maintain a monotonic decreasing deque.
* Shriking window (l++): Pop out the index that smaller than l from front of deque.
* The index on the front of deque is the index of the maximum value.
``` cpp
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        int n = nums.size();
        deque<int> dq;
        // l = r-(k-1)
        vector<int> ans;
        for(int r = 0; r < n; r++) {
            while(dq.size() && dq.front()<r-(k-1)) dq.pop_front(); // use if is ok
            while(dq.size() && nums[dq.back()]<=nums[r]) dq.pop_back();
            dq.push_back(r);
            if(r>=k-1) ans.push_back(nums[dq.front()]);
        }
        return ans;
    }
};
```

### Longest Continuous Subarray With Absolute Diff Less Than or Equal to Limit
* https://leetcode.com/problems/longest-continuous-subarray-with-absolute-diff-less-than-or-equal-to-limit/description/
* Sliding Window + Monotonic Deque
* Monotonic: window larger then max-min larger, window smaller then max-min smaller
``` cpp
class Solution {
public:
    int longestSubarray(vector<int>& nums, int limit) {
        int n = nums.size();
        int l = 0;
        deque<int> dq_min, dq_max;
        int ans = 0;
        for(int r = 0; r < n; r++) {
            while(dq_min.size() && nums[dq_min.back()]>nums[r]) dq_min.pop_back();
            while(dq_max.size() && nums[dq_max.back()]<nums[r]) dq_max.pop_back();
            dq_min.push_back(r);
            dq_max.push_back(r);
            while(nums[dq_max.front()]-nums[dq_min.front()]>limit) {
                while(dq_min.size() && dq_min.front()<=l) dq_min.pop_front(); // use if is ok
                while(dq_max.size() && dq_max.front()<=l) dq_max.pop_front(); // use if is ok
                l++;
            }
            ans = max(ans, r-l+1);
        }
        return ans;
    }
};
```
