# Sort
* https://leetcode.com/problems/sort-an-array/

## Insertion Sort
* Time: O(n^2), Space(1)
* Works perfect if your n is small
``` cpp
    vector<int> sortArray(vector<int>& nums) {
        int n = nums.size();
        if(n<=1) return nums;
        for(int i = 1; i < n; i++) {
            int j = i;
            while(j-1>=0 && nums[j]<nums[j-1]) {
                swap(nums[j], nums[j-1]);
                j--;
            }
        }
        return nums;
    }
```

## Merge Sort
* Time: O(nlogn), Space: O(n)
``` cpp
// Merge Sort Iterative Version
vector<int> sortArray(vector<int>& nums) {
    int n = nums.size();
    vector<int> tmp(n);
    auto merge = [&](int l, int mid, int r) -> void {
        int l1 = l, r1 = mid+1, i = l;
        while(l1<=mid && r1<=r) {
            if(nums[l1]<=nums[r1]) tmp[i++] = nums[l1++];
            else tmp[i++] = nums[r1++];
        }
        while(l1<=mid) tmp[i++] = nums[l1++];
        while(r1<=r) tmp[i++] = nums[r1++];
        for(int i = l; i <= r; i++) nums[i] = tmp[i];
    };
    for(int step = 1; step < n; step *= 2) {
        int l = 0;
        while(l < n) {
            int mid = l+step-1;
            if(mid+1>=n) break;
            int r = min(mid+step, n-1);
            merge(l, mid, r);
            l = r+1;
        }
    }
    return nums;
}
// Merge Sort Recursive Veversion
vector<int> sortArray(vector<int>& nums) {
    int n = nums.size();
    vector<int> tmp(n);
    auto merge = [&](int l, int mid, int r) -> void {
        int l1 = l, r1 = mid+1, i = l;
        while(l1<=mid && r1<=r) {
            if(nums[l1]<=nums[r1]) tmp[i++] = nums[l1++];
            else tmp[i++] = nums[r1++];
        }
        while(l1<=mid) tmp[i++] = nums[l1++];
        while(r1<=r) tmp[i++] = nums[r1++];
        for(int i = l; i <= r; i++) nums[i] = tmp[i];
    };
    auto msort = [&](auto &&self, int l, int r) -> void {
        if(l==r) {
            return;
        }
        int mid = (l+r)/2;
        self(self, l, mid);
        self(self, mid+1, r);
        merge(l, mid, r);
    };
    msort(msort, 0, n-1);
    return nums;
}
```

## Quick Sort
* https://www.bilibili.com/video/BV1cc411F7Y6
* Time: O(nlogn), Space: O(logn) for the recursive depth
``` cpp
vector<int> sortArray(vector<int>& nums) {
    auto rng = [](int start, int end) -> int {
        static std::mt19937 gen(
            std::chrono::steady_clock::now().time_since_epoch().count());
        return std::uniform_int_distribution<int>(start, end)(gen);
    };
    // ....first.....last......
    //  <x       ==x        >x
    auto partition = [&](int l, int r, int x) -> pair<int, int> {
        int first = l, last = r, i = l;
        while(i<=last) {
            if(nums[i]==x) i++;
            else if(nums[i]<x) swap(nums[first++], nums[i++]);
            else swap(nums[last--], nums[i]);
        }
        return {first, last};
    };
    auto qsort = [&](auto &&self, int l, int r) -> void {
        if(l>=r) return;
        int x = nums[rng(l, r)];
        auto [first, last] = partition(l, r, x);
        self(self, l, first-1);
        self(self, last+1, r);
    };
    int n = nums.size();
    qsort(qsort, 0, n-1);
    return nums;
}
```
### Find Kth smallest element by modified quick sort
* https://leetcode.com/problems/kth-largest-element-in-an-array/description/
* https://www.bilibili.com/video/BV1mN411b71K/
* C++ builtin function nth_element(StartIterator, NthIterator, EndIterator) can be used to easily solved this problem
* Time: O(n), Space: O(1)
``` cpp
    // solved by modified quick sort, iterative version
    int findKthLargest(vector<int>& nums, int k) {
        auto rng = [](int start, int end) -> int {
            static std::mt19937 gen(
                std::chrono::steady_clock::now().time_since_epoch().count());
            return std::uniform_int_distribution<int>(start, end)(gen);
        };
        auto partition = [&](int l, int r, int x) -> pair<int, int> {
            int first = l, last = r, i = l;
            while(i<=last) {
                if(nums[i]==x) i++;
                else if(nums[i]<x) swap(nums[first++], nums[i++]);
                else swap(nums[last--], nums[i]);
            }
            return {first, last};
        };
        // find the (id+1)th smallest number in nums[l:r]
        auto helper = [&](auto &&self, int l, int r, int id) -> int {
            while(l<=r) {
                int x = nums[rng(l, r)];
                auto [first, last] = partition(l, r, x);
                if(last<id) l = last+1;
                else if(id<first) r = first-1;
                else return nums[id];
            }
            assert(0);
        };
        int n = nums.size();
        return helper(helper, 0, n-1, n-k);
    }
    // // solved by modified quick sort, recursive version
    // int findKthLargest(vector<int>& nums, int k) {
    //     auto rng = [](int start, int end) -> int {
    //         static std::mt19937 gen(
    //             std::chrono::steady_clock::now().time_since_epoch().count());
    //         return std::uniform_int_distribution<int>(start, end)(gen);
    //     };
    //     auto partition = [&](int l, int r, int x) -> pair<int, int> {
    //         int first = l, last = r, i = l;
    //         while(i<=last) {
    //             if(nums[i]==x) i++;
    //             else if(nums[i]<x) swap(nums[first++], nums[i++]);
    //             else swap(nums[last--], nums[i]);
    //         }
    //         return {first, last};
    //     };
    //     // find the (id+1)th smallest number in nums[l:r]
    //     auto helper = [&](auto &&self, int l, int r, int id) -> int {
    //         int x = nums[rng(l, r)];
    //         auto [first, last] = partition(l, r, x);
    //         if(last<id) return self(self, last+1, r, id);
    //         else if(id<first) return self(self, l, first-1, id);
    //         else return nums[id];
    //     };
    //     int n = nums.size();
    //     return helper(helper, 0, n-1, n-k);
    // }
    // // use the c++ builtin function
    // // nth_element(StartIterator, NthIterator, EndIterator): the (n+1)th smallest value will be put in NthIterator
    // int findKthLargest(vector<int>& nums, int k) {
    //     int n = nums.size();
    //     auto iter = nums.begin()+(n-k);
    //     nth_element(nums.begin(), iter, nums.end());
    //     return *iter;
    // }
```

## Heap Sort
* https://www.bilibili.com/video/BV1fu4y1q77y
* Max Heap
* Time: O(nlogn), Space: O(1)
``` cpp
    vector<int> sortArray(vector<int>& nums) {
        auto up = [&](int i) -> void {
            while(i>0 && nums[i]>nums[(i-1)/2]) {
                swap(nums[i], nums[(i-1)/2]);
                i = (i-1)/2;
            }
        };
        auto down = [&](int i, int size) -> void {
            while(i<size) {
                int l = 2*i+1;
                int r = l+1;
                int besti = i;
                if(l<size && nums[l]>nums[besti]) besti = l;
                if(r<size && nums[r]>nums[besti]) besti = r;
                if(i==besti) break;
                swap(nums[i], nums[besti]);
                i = besti;
            }
        };

        int n = nums.size();

        ///////////////////// Create Heap
        // for(int i = 0; i < n; i++) up(i); // O(nlogn)
        for(int i = n-1; i >= 0; i--) down(i, n); // O(n)

        /////////////////// Put the max element to last
        int size = n;
        while(size>1) {
            swap(nums[0], nums[size-1]);
            size--;
            down(0, size);
        }
        return nums;
    }
```

## Radix Sort
* https://www.bilibili.com/video/BV1Lh4y1c7Aw
* Time: O(n), Space: O(base)
* Only works for non-negative numbers
``` cpp
    vector<int> sortArray(vector<int>& nums) {
        // only work for non-negative numbers
        auto rsort = [](vector<int> &nums, int base) -> void {
            int n = nums.size();
            int mx = 0;
            for(int i = 0; i < n; i++) mx = max(mx, nums[i]);
            int round = 0;
            while(mx>0) {
                mx /= base;
                round++;
            }
            vector<int> help(n);
            vector<int> freq(base);
            int div = 1;
            while(round--) {
                freq.clear();
                freq.resize(base);
                for(int i = 0; i < n; i++) freq[nums[i]/div%base]++;
                for(int i = 1; i < base; i++) freq[i] += freq[i-1];
                for(int i = n-1; i >= 0; i--) {
                    help[freq[nums[i]/div%base]-1] = nums[i]; 
                    freq[nums[i]/div%base]--;
                }
                for(int i = 0; i < n; i++) nums[i] = help[i];
                div *= base;
            }
        };
        // shift to make all the numbers be non-negative
        int mn = 1e9;
        int n = nums.size();
        for(int i = 0; i < n; i++) mn = min(mn, nums[i]);
        for(int i = 0; i < n; i++) nums[i] -= mn;
        rsort(nums, 10);
        // shift back
        for(int i = 0; i < n; i++) nums[i] += mn;
        return nums;
    }
```
