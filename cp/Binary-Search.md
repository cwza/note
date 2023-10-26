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
