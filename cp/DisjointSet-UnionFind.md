# Disjoint Set Union Find
* Time Complexity: O(alpha(n)) ~ O(1)
    + when n = 10^80, alpha(n) <= 6, so we can treat it as O(1)
* Union Find is very useful to group nodes on un-directive graph (For directive graph use SCC)

## Implementation
* 2 Optimizations:
    + Path Compression while find (The most important optimization, must do)
    + Union By Size while union(Size estimate tree height): small point to large (Optional optimization)
* https://www.nowcoder.com/practice/e7ed657974934a30b2010046536a5372
* Use i==find(i) to check whether i is a leader node 
* You can add some information to leader node, just like the sz array in following implementation.
```cpp
// Implement 2 Optimization
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
        lk[x] = find(lk[x]); //  path compression
        return lk[x];
    }
    void unite(int x, int y) {
        x = find(x);
        y = find(y);
        if(x==y) return;
        if(sz[x]>sz[y]) swap(x, y); // sz[x] <= sz[y]
        lk[x] = y; // x -> y  // union by size
        sz[y] += sz[x];
        total_sz--;
    }
    int size(int x) {
        return sz[find(x)];
    }
};


// Implement only Path Compression
struct DSU {
    vector<int> lk, sz;
    int total_size;
    DSU(int n): lk(n), sz(n, 1), total_size(n) {
        iota(lk.begin(), lk.end(), 0);
    }
    int find(int x) {
        if(lk[x]==x) return x;
        lk[x] = find(lk[x]);
        return lk[x];
    }
    void unite(int x, int y) {
        x = find(x);
        y = find(y);
        if(x!=y) {
            total_size--;
            lk[x] = y;
            sz[y] += sz[x];
        }
    }
};
```

## Practice
- [D - Friend Suggestions (atcoder.jp)](https://atcoder.jp/contests/abc157/tasks/abc157_d)
