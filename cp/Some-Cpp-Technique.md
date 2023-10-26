# Some C++ Technique

## Custom Hash Map
- gp_hash_table is much faster than unordered_map or map
```cpp
#include <ext/pb_ds/assoc_container.hpp>
using namespace __gnu_pbds;
struct my_hash {
    const uint64_t RANDOM = chrono::steady_clock::now().time_since_epoch().count();
    static uint64_t splitmix64(uint64_t x) {
        x += 0x9e3779b97f4a7c15;
        x = (x ^ (x >> 30)) * 0xbf58476d1ce4e5b9;
        x = (x ^ (x >> 27)) * 0x94d049bb133111eb;
        return x ^ (x >> 31);
    }
    size_t operator()(uint64_t x) const {
        return splitmix64(x + RANDOM);
    }
};

int main() {
    gp_hash_table<long long,int,my_hash> dp;
}
```

## Uniquify Array
```cpp
vector<int> divisors = {1,2,3,3,2,2};
sort(divisors.begin(), divisors.end());
// unique will try to rearrange array, put the contiguos repeated elements to the last of the array, and return the last iterator
divisors.resize(unique(divisors.begin(), divisors.end())-divisors.begin());
for(int divisor : divisors) cout << divisor << " "; // 1,2,3
```
