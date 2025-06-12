# Stress Test

## Simple Example
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

long long rng(long long from, long long to) {
	static std::mt19937 gen(
	    std::chrono::steady_clock::now().time_since_epoch().count());
	return std::uniform_int_distribution<long long>(from, to)(gen);
}

vector<int> gen_random_arr(int len, int max_val) {
    vector<int> arr(len);
    for(int i = 0; i < len; i++) {
        arr[i] = rng(0, max_val);
    }
    return arr;
}

void insertion_sort(vector<int> &a) {
    int n = a.size();
    for(int i = 0; i < n; i++) {
        int j = i;
        while(j-1>=0 && a[j]<a[j-1]) {
            swap(a[j], a[j-1]);
            j--;
        }
    }
}

int main() {
    int max_len = 10;
    int max_val = 100;
    int round = 10;
    for(int i = 0; i < round; i++) {
        int len = rng(0, max_len);
        vector<int> origin_arr = gen_random_arr(len, max_val);
        vector<int> insert_arr = origin_arr;
        vector<int> q_arr = origin_arr;
        sort(q_arr.begin(), q_arr.end());
        insertion_sort(insert_arr);
        if(q_arr!=insert_arr) {
            dbg(origin_arr);
            dbg(q_arr);
            dbg(insert_arr);
            cout << "----------\n";
        }
    }
}
```
