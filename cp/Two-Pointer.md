# Two Pointer
##  Sliding Window
* https://leetcode.com/problems/minimum-size-subarray-sum/description/
* Can be used on many sub-string and sub-array problems
* The range of sub-string and the answer need to have some monotonic relation
* Two Method
    + Calculate each answer for each l
    + Calculate each answer for each r
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
            while(cur-nums[l]>=target) {
                cur -= nums[l];
                l++;
            }
            if(cur>=target) ans = min(ans, r-l+1);
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

## Fast Slow Pointer
* https://leetcode.com/problems/linked-list-cycle-ii/description/
``` cpp
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        if(!head || !head->next) return nullptr;
        auto slow = head, fast = head;
        while(1) {
            if(!fast || !fast->next) return nullptr;
            slow = slow->next;
            fast = fast->next->next;
            if(slow==fast) break;
        }

        fast = head;
        while(slow!=fast) {
            slow = slow->next;
            fast = fast->next;
        }
        return slow;
    }
};
```
