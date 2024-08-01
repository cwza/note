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

## Find the kth smallest value in a sorted array
* O(logV * logN), V: value range, N: array length
* There are at least k elements smaller or equal to the kth smallest value;
* Define f(x): the number of elements which is smaller or equal to x, we want to find the smallest x such that f(x) >= k
    + We can find f(x) in log(n) by binary search again. (use )
* ex: [1, 1, 2, 3, 4, 4, 4, 5, 6, 7], we binary search from 1 to 7
    + the 3rd smallest value is 2, and there are 3 elements before it, f(1)=2<3 but f(2) = 3 >= 3
    + the 6th smallest value is 4, and there are 7 elements before it, f(3)=4< 6 but f(4) = 7 >= 6
``` cpp
int k = 6;
vector<int> a = {1, 1, 2, 3, 4, 4, 4, 5, 6, 7}
int lb = 1, rb = 7;
int ans = -1;
while(lb<=rb) { // logV
    int mid = lb + (rb-lb)/2;
    int cnt = upper_bound(a.begin(), a.end(), mid)-a.begin(); // logN
    assert(cnt>=0);
    if(cnt>=k) {
        ans = mid;
        rb = mid-1;
    } else lb = mid+1;
}
assert(ans!=-1);
cout << ans << "\n"; // 4
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
