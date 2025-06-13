# Divide and Conquer
## Merge Sort
* Good Template for Many Divide and Conquer Problems
``` cpp
#include <bits/stdc++.h>
using namespace std;
#ifdef DEBUG
    #include "./tool/debug.hpp"
#else
    #define dbg(...)
    #define dbgarr(a)
#endif
using ll = long long;

void solve() {
    int n;
    cin >> n;
    vector<int> a(n);
    for(int i = 0; i < n; i++) cin >> a[i];

    vector<int> tmp(n);
    auto msort = [&](auto &&self, int l, int r) -> void {
        if(l==r) return;
        int mid = l+(r-l)/2;
        self(self, l, mid);
        self(self, mid+1, r);

        // merge
        int l1 = l, r1 = mid+1, idx = l;
        while(l1<=mid && r1<=r) {
            if(a[l1]<=a[r1]) tmp[idx++] = a[l1++];
            else tmp[idx++] = a[r1++];
        }
        while(l1<=mid) tmp[idx++] = a[l1++];
        while(r1<=r) tmp[idx++] = a[r1++];
        for(int i = l; i <= r; i++) a[i] = tmp[i];
    };
    msort(msort, 0, n-1);
    for(int i = 0; i < n; i++) cout << a[i] << " ";
    cout << "\n";
}


int main() {
    solve();
}
```

## Reverse Pairs
* https://leetcode.com/problems/reverse-pairs/
``` cpp
using ll = long long;

class Solution {
public:
    int reversePairs(vector<int>& nums) {
        int n = nums.size();
        vector<int> tmp(n);
        auto msort = [&](auto &&self, int l, int r) -> int {
            if(l==r) return 0;
            int mid = l+(r-l)/2;
            int ans = self(self, l, mid) + self(self, mid+1, r);
    
            // calculate
            int l1 = mid;
            int curans = 0;
            for(int i = r; i >= mid+1; i--) {
                while(l1>=l && nums[l1]>2ll*nums[i]) {
                    curans++;
                    l1--;
                }
                ans += curans;
            }

            // merge part same as merge sort
            l1 = l;
            int r1 = mid+1, idx = l;
            while(l1<=mid && r1<=r) {
                if(nums[l1]<=nums[r1]) tmp[idx++] = nums[l1++];
                else tmp[idx++] = nums[r1++];
            }
            while(l1<=mid) tmp[idx++] = nums[l1++];
            while(r1<=r) tmp[idx++] = nums[r1++];
            for(int i = l; i <= r; i++) nums[i] = tmp[i];

            return ans;
        };
        return msort(msort, 0, n-1);
    }
};
```
