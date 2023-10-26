# Disjoint Set, Union Find

```cpp
struct DST {
    vector<int> sz, lk;
		int total_sz;
    DST(int n): sz(n), lk(n), total_sz(n) {
        for(int i = 0; i < n; i++) {
            sz[i] = 1;
            lk[i] = i;
        }
    }
    int find(int x) {
        while(lk[x]!=x) {
            x = lk[x];
        }
        return x;
    }
    bool unite(int x, int y) {
        x = find(x);
        y = find(y);
        if(x==y) return false;
        if(sz[x]>sz[y]) swap(x, y);
        lk[x] = y;
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
