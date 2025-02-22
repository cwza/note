# Disjoint Set, Union Find

```cpp
struct DSU {
    vector<int> sz, lk;
    int total_sz;
    DSU(int n): sz(n), lk(n), total_sz(n) {
        for(int i = 0; i < n; i++) {
            sz[i] = 1;
            lk[i] = i;
        }
    }
    int find(int x) {
        if(x==lk[x]) return x;
        lk[x] = find(lk[x]);
        return lk[x];
    }
    bool unite(int x, int y) {
        x = find(x);
        y = find(y);
        if(x==y) return false;
        if(sz[x]>sz[y]) swap(x, y); // sz[x] <= sz[y]
        lk[x] = y; // x -> y
        sz[y] += sz[x];
        total_sz--;
        return true;
    }
    int size(int x) {
        return sz[find(x)];
    }
};
```

## Practice
- [D - Friend Suggestions (atcoder.jp)](https://atcoder.jp/contests/abc157/tasks/abc157_d)
