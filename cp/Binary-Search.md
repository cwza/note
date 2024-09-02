[[toc]]

# Binary Search
- [(20) Binary Search tutorial (C++ and Python) - YouTube](https://www.youtube.com/watch?v=GU7DpgHINWQ)

## Generally what kind of problem can be solved using binary search
- Suppose we have a sequence like this: FFFFFTTTTT
- Find the first T in this sequence or Find the last F in this sequence
- Generally: Find the boundary position

## Find the exactly target on sorted array
```cpp
vector<int> arr = {2,3,5,6,8,10,12};
int target = 10;

int lb = 0, rb = n-1;
while(lb<=rb) {
    int mid = lb + (rb-rb)/2;
    if(arr[mid]==target) return mid;
    else if(arr[mid]<target) lb = mid+1;
    else rb = mid-1;
}
return -1;
```

## Find the first element that greater than or equal to target on sorted array
```cpp
//////////         F F F F F T  T
vector<int> arr = {2,3,5,6,8,10,12};
int target = 10;

int lb = 0, rb = n-1;
int ans = -1;
while(lb<=rb) {
    int mid = lb + (rb-lb)/2;
    if(arr[mid]>=target) { // This condition makes your array becomes FFFFFTT, then we just want to find the first T
        ans = mid;
        rb = mid-1;
    } else {
        lb = mid+1;
    } 
}
return ans;
```

## Find the maximum element in increasing then decreasing array
```cpp
////////           T T T T T T  F  F F F F
vector<int> arr = {2,3,4,6,9,12,11,8,6,4,1};

int lb = 0, rb = n-1;
int ans = -1;
while(lb<=rb) {
    int mid = lb + (rb-lb)/2;
    if(mid==0 || arr[mid]>arr[mid-1]) { // This condition makes your array becomes TTTTTTFFFFF, then we just want to find the last T
        ans = mid;
        lb = mid+1;
    } else {
        rb = mid-1;
    } 
}
return ans;
```

## Find sqrt(x) with some precision (Binary search on float value)
```cpp
double EPS = 0.00000001;
int x = 4;

double lb = 0, rb = x;
while(rb-lb > EPS) {
    double mid = lb + (rb-lb)/2;
    if(mid*mid<=x) {
        lb = mid;
    } else {
        rb = mid;
    } 
}
return lb+(rb-lb)/2;
```

## Find the kth smallest value in an non-sorted array
* Constraint: The values of array are in range [1, 100000]
    + so that we can use bucket to count the frequency of each number
* Key: There are **at least** k elements smaller or equal to the kth smallest value!!
* Define pref_cnt(x): the number of elements which is smaller or equal to x, we want to find the smallest x such that pref_cnt(x) >= k
    + Actually the pref_cnt is just the prefix sum of cnt(x), which cnt(x) is defined as the frequency of x
* We binary search on values to solve this problem in logV, V is the value range
* Take an example: [1, 1, 2, 3, 4, 4, 4, 5, 6, 7], binary search from 1 to 7, note that this array does not need to be sorted
    + the 3rd smallest value is 2, and there are 3 elements before it, pref_cnt(1)=2<3 but pref_cnt(2) = 3 >= 3
    + the 6th smallest value is 4, and there are 7 elements before it, pref_cnt(3)=4< 6 but pref_cnt(4) = 7 >= 6
* You can use this same idea to find Median in logV. Median is just the (n+2)/2th smallest value in an array.
``` cpp
int main() {
    int n, k; // n = 10, k = 6
    cin >> n >> k;
    const int maxV = 1e5;
    vector<int> a(n); // [1, 1, 2, 3, 4, 4, 4, 5, 6, 7]
    vector<int> cnt(maxV+1); // [0, 2, 1, 1, 3, 1, 1, 1]
    for(int i = 0; i < n; i++) {
        cin >> a[i];
        cnt[a[i]]++;
    }
    vector<int> pref_cnt(maxV+1); // [0, 2, 3, 4, 7, 8, 9, 10]
    for(int i = 1; i <= maxV; i++) pref_cnt[i] = pref_cnt[i-1] + cnt[i];

    int lb = 1, rb = maxV;
    int ans = -1;
    while(lb<=rb) { // logV
        int mid = lb + (rb-lb)/2;
        if(pref_cnt[mid]>=k) { // pref_cnt[4]==7 which is the first one that >= 7
            ans = mid;
            rb = mid-1;
        } else lb = mid+1;
    }
    assert(ans!=-1);
    cout << ans << "\n"; // 4
}
```

# Ternary Search

## Find the minimum value of some unimodal function
```cpp
double EPS = 0.00000001;

void func(double x) {
    return x*x;
}

double lb = 0, rb = 1e9;
while(rb-lb > EPS) {
    double ml = lb+(rb-lb)/3;
    double mr = rb-(rb-lb)/3;
    if(func(ml)<=func(mr)) rb = mr;
    else lb = ml;
}
return lb+(rb-lb)/2;
```
