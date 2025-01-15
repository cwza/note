# Static Range Query by Sparse Table
- O(NlogN) to build, O(1) to query
- sparse[i][j] = min(sparse[i][j-1], sparse[i+(1<<(j-1))][j-1])
    - minimum value that from inp[i] to inp[i+2^j-1], 2^j is the length
- min(sparse[l][k], sparse[r-(1<<k)+1][k])
    - k = floor( lg(r-l+1) ) = 31 - __builtin_clz(r-l+1)
- can be used on min, max, xor, gcd (can't be used on sum)
- [(16) Sparse Table & RMQ (Range Minimum Query) - YouTube](https://www.youtube.com/watch?v=0jWeUdxrGm4&list=PLl0KD3g-oDOHpWRyyGBUJ9jmul0lUOD80&index=18)
- [CSES - Static Range Minimum Queries](https://cses.fi/problemset/task/1647/)

```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;

struct SparseTable {
    vector<vector<int>> sparse;
    SparseTable(vector<int> &inp) {
        int n = inp.size();
        int logn = 1;
        for(int i = 2; i < n; i*=2) logn++;
        sparse.resize(n, vector<int>(logn+1));
        for(int j = 0; j <= logn; j++) {
            for(int i = 0; i+(1<<j)-1 < n; i++) {
                if(j==0) sparse[i][0] = inp[i];
                else {
                    sparse[i][j] = min(sparse[i][j-1], sparse[i+(1<<(j-1))][j-1]); // change me
                }
            }
        }
    }
    int query(int l, int r) {
        int k = 31 - __builtin_clz(r-l+1);
        return min(sparse[l][k], sparse[r-(1<<k)+1][k]); // change me
    }
};

int main() {
    int n, q;
    cin >> n >> q;
    vector<int> inp(n);
    for(int i = 0; i < n; i++) cin >> inp[i];

    auto sparse_table = SparseTable(inp);
    while(q--) {
        int a, b;
        cin >> a >> b;
        a--; b--;
        cout << sparse_table.query(a, b) << "\n";
    }
}
```
