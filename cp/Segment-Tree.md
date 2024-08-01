[[toc]]

# Segment Tree
- [(28) Segment Tree (Implementation) - YouTube](https://www.youtube.com/watch?v=2FShdqn-Oz8&list=WL&index=5&t=32s)
- O(2*N) for build (both time and space)
    - N = the nearest power of 2 to the original input size (ex: original size=6 → N=8)
- O(logN) for update and query

## Point Update + Range Query
- [CSES - Dynamic Range Sum Queries](https://cses.fi/problemset/result/10016275/)
```cpp
#include <bits/stdc++.h>
using namespace std;
#ifdef DEBUG
    #include "debug.hpp"
#else
    #define dbg(x)
#endif
using ll = long long;

struct SegmentTree {
    int n;
    vector<ll> tree;
    SegmentTree(vector<int> inp) {
        n = inp.size();
        while(__builtin_popcount(n)!=1) n++;
        tree.resize(2*n);
        for(int i = 0; i < n && i < (int)inp.size(); i++) tree[n+i] = inp[i];
        for(int i = n-1; i >= 1; i--) tree[i] = tree[2*i] + tree[2*i+1]; // change this
    }
    ll _query(int node, int node_l, int node_r, int query_l, int query_r) {
        if(node_r<query_l || node_l>query_r) { // node disjoint query
            return 0;
        }
        if(node_l>=query_l && node_r<=query_r) { // query full cover node
            return tree[node];
        }
        int mid = (node_l + node_r)/2;
        ll ansl = _query(2*node, node_l, mid, query_l, query_r);
        ll ansr = _query(2*node+1, mid+1, node_r, query_l, query_r);
        return ansl + ansr;
    }
    void _update(int node, int node_l, int node_r, int query_l, int query_r, int val) {
        if(node_r<query_l || node_l>query_r) {
            return;
        }
        if(node_l>=query_l && node_r<=query_r) {
            tree[node] = val;
            return;
        }
        int mid = (node_l + node_r)/2;
        _update(2*node, node_l, mid, query_l, query_r, val);
        _update(2*node+1, mid+1, node_r, query_l, query_r, val);
        tree[node] = tree[2*node] + tree[2*node+1];
    }
    ll query(int l, int r) {
        return _query(1, 0, n-1, l, r);
    }
    void update(int l, int val) {
        return _update(1, 0, n-1, l, l, val);
    }
};


void solve() {
    int n, q;
    cin >> n >> q;
    vector<int> inp(n);
    for(int i = 0; i < n; i++) cin >> inp[i];
    
    SegmentTree segtree(inp);
    while(q--) {
        int mode, a, b;
        cin >> mode >> a >> b;
        if(mode==1) {
            a--;
            segtree.update(a, b);
        } else {
            a--; b--;
            cout << segtree.query(a, b) << "\n";
        }
    }
}
 
bool multi = 0;
int main() {
    ios_base::sync_with_stdio(0);
    cin.tie(0);
 
    int t = 1;
    if (multi)
        cin >> t;
    for (int i = 0; i < t; i++) {
        solve();
    }
}
```

## Range Update + Point Query by Difference Array Concept
- [CSES - Range Update Queries](https://cses.fi/problemset/task/1651)
```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
 
 
void IO(string name = "") {
	cin.tie(0)->sync_with_stdio(0);
	if (name.size()) {
		freopen((name + ".in").c_str(), "r", stdin);
		freopen((name + ".out").c_str(), "w", stdout);
	}
}
 
struct SegmentTree {
    int n;
    vector<ll> tree;
    SegmentTree(vector<int> &inp) {
        n = inp.size();
        while(__builtin_popcount(n)!=1) n++;
        tree.resize(2*n);
        for(int i = 0; i < n; i++) tree[i+n] = inp[i];
        for(int i = n-1; i >= 1; i--) tree[i] = tree[2*i] + tree[2*i+1];
    }
    void update(int i, int val) {
        i += n;
        tree[i] += val;
        while(i/2) {
            i /= 2;
            tree[i] = tree[2*i] + tree[2*i+1];
        }
    }
    ll query(int l, int r) {
        l += n; r += n;
        ll ans = 0;
        while(l <= r) {
            if(l%2==1) ans += tree[l++]; 
            if(r%2==0) ans += tree[r--];
            l /= 2; r /= 2;
        }
        return ans;
    }
};
 
int main() {
    // IO("balancing");
 
    int n, q;
    cin >> n >> q;
    vector<int> inp(n);
    for(int i = 0; i < n; i++) cin >> inp[i];
    vector<int> diff(n); diff[0] = inp[0];
    for(int i = 1; i < n; i++) diff[i] = inp[i]-inp[i-1];
 
    auto segtree = SegmentTree(diff);
    while(q--) {
        int mode;
        cin >> mode;
        if(mode==1) {
            int a, b, u;
            cin >> a >> b >> u;
            a--; b--;
            segtree.update(a, u);
            if(b+1<n)
                segtree.update(b+1, -u);
        } else {
            int k;
            cin >> k;
            k--;
            cout << segtree.query(0, k) << "\n";
        }
    }
}
```

## Range Update + Range Query by Lazy Propagation
- [CSES - Range Updates and Sums](https://cses.fi/problemset/task/1735/)
```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
 
 
void IO(string name = "") {
	cin.tie(0)->sync_with_stdio(0);
	if (name.size()) {
		freopen((name + ".in").c_str(), "r", stdin);
		freopen((name + ".out").c_str(), "w", stdout);
	}
}

struct SegmentNode {
    ll val;
    ll lazy;
    bool mode; // 1: set, 0: add
};

struct SegmentTree {
    int n;
    vector<SegmentNode> tree;
    SegmentTree(vector<int> &inp) {
        n = inp.size();
        while(__builtin_popcount(n)!=1) n++;
        tree.resize(2*n);
        for(int i = 0; i < n; i++) tree[n+i].val = inp[i];
        for(int i = n-1; i >= 1; i--) tree[i].val = tree[2*i].val + tree[2*i+1].val;
    }
    void apply(int node, int node_l, int node_r, ll val, bool mode) {
        if(mode) {
            tree[node].val = val*(node_r-node_l+1);
            tree[node].lazy = val;
            tree[node].mode = 1;
        } else {
            tree[node].val += val*(node_r-node_l+1);
            tree[node].lazy += val;
        }
    }
    void push(int node, int node_l, int mid, int node_r) {
        apply(2*node, node_l, mid, tree[node].lazy, tree[node].mode);
        apply(2*node+1, mid+1, node_r, tree[node].lazy, tree[node].mode);
        tree[node].lazy = 0;
        tree[node].mode = 0;
    }
    ll _query(int node, int node_l, int node_r, int query_l, int query_r) {
        if(node_r<query_l || node_l>query_r) { // node disjoint query
            return 0;
        }
        if(node_l>=query_l && node_r<=query_r) { // query full cover node
            return tree[node].val;
        }
        int mid = (node_l + node_r)/2;
        push(node, node_l, mid, node_r);
        ll ansl = _query(2*node, node_l, mid, query_l, query_r);
        ll ansr = _query(2*node+1, mid+1, node_r, query_l, query_r);
        return ansl + ansr;
    }
    void _update(int node, int node_l, int node_r, int query_l, int query_r, int val, bool mode) {
        if(node_r<query_l || node_l>query_r) {
            return;
        }
        if(node_l>=query_l && node_r<=query_r) {
            apply(node, node_l, node_r, val, mode);
            return;
        }
        int mid = (node_l + node_r)/2;
        push(node, node_l, mid, node_r);
        _update(2*node, node_l, mid, query_l, query_r, val, mode);
        _update(2*node+1, mid+1, node_r, query_l, query_r, val, mode);
        tree[node].val = tree[2*node].val + tree[2*node+1].val;
    }
    ll query(int l, int r) {
        return _query(1, 0, n-1, l, r);
    }
    void update(int l, int r, int val, bool mode) {
        return _update(1, 0, n-1, l, r, val, mode);
    }
};

int main() {
    // IO("balancing");

    int n, q;
    cin >> n >> q;
    vector<int> inp(n);
    for(int i = 0; i < n; i++) cin >> inp[i];

    auto segtree = SegmentTree(inp);
    while(q--) {
        int mode;
        cin >> mode;
        if(mode==1) {
            int a, b, x;
            cin >> a >> b >> x;
            a--; b--;
            segtree.update(a, b, x, 0);
        } else if(mode==2) {
            int a, b, x;
            cin >> a >> b >> x;
            a--; b--;
            segtree.update(a, b, x, 1);
        } else {
            int a, b;
            cin >> a >> b;
            a--; b--;
            cout << segtree.query(a, b) << "\n";
        }
    }
}
```

## Range Query the smallest value and it's frequency
* SegmentNode stores {smallest value, freguency}
* When combine 2 segment use following logic
    + if(smallest value of left segment == smallest value of right segment) return {smallest value of left, sum of freguency}
    + else return min(left segement, right segment)
* When add x to segement, just add x to the smallest value and frequency remains the same.

## Solve Subtree Query by RMQ
- [CSES - Subtree Queries](https://cses.fi/problemset/task/1137)
```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
 
 
void IO(string name = "") {
	cin.tie(0)->sync_with_stdio(0);
	if (name.size()) {
		freopen((name + ".in").c_str(), "r", stdin);
		freopen((name + ".out").c_str(), "w", stdout);
	}
}
 
/*
Idea from CPH
    0
   . .
  1   2
     . .
    3   4
 
            0 1 2 3 4 (original index)
starttime   0 1 2 3 4 (index in segment tree)
endtime     4 1 4 3 4
value       4 2 5 2 1
subtree size = endtime-starttime-1
*/
 
struct SegmentTree {
    int n;
    vector<ll> tree;
    SegmentTree(int inp[], int _n) {
        n = _n;
        while(__builtin_popcount(n)!=1) n++;
        tree.resize(2*n);
        for(int i = 0; i < n; i++) tree[n+i] = inp[i];
        for(int i = n-1; i >= 1; i--) tree[i] = tree[2*i] + tree[2*i+1];
    }
    ll _query2(int l, int r) {
        l += n; r += n;
        ll ans = 0;
        while(l <= r) {
            if(l%2==1) ans += tree[l++];
            if(r%2==0) ans += tree[r--];
            l /= 2; r /= 2;
        }
        return ans;
    }
    void _update2(int i, int val) {
        i += n;
        tree[i] = val;
        while(i/2) {
            i/=2;
            tree[i] = tree[2*i] + tree[2*i+1];
        }
    }
    ll query(int l, int r) {
        return _query2(l, r);
    }
    void update(int i, int val) {
        return _update2(i, val);
    }
};
 
const int maxN = 2e5;
int N, Q;
int values[maxN], visited[maxN];
vector<int> adj[maxN];
int t;
int segtree_arr[maxN], starttime[maxN], endtime[maxN];
 
void dfs(int u, int p) {
    visited[u] = 1;
    segtree_arr[t] = values[u];
    starttime[u] = t;
    t++;
    for(int v : adj[u]) {
        if(v==p) continue;
        if(!visited[v]) {
            dfs(v, u);
        }
    }
    endtime[u] = t-1;
}
 
int main() {
    // IO("balancing");
 
    cin >> N >> Q;
    for(int i = 0; i < N; i++) cin >> values[i];
    for(int i = 0, a, b; i < N-1; i++) {
        cin >> a >> b;
        a--; b--;
        adj[a].push_back(b);
        adj[b].push_back(a);
    }
    dfs(0, -1);
    auto segtree = SegmentTree(segtree_arr, N);
 
    while(Q--) {
        int mode;
        cin >> mode;
        if(mode==1) {
            int s, x;
            cin >> s >> x;
            s--;
            segtree.update(starttime[s], x);
        } else {
            int s;
            cin >> s;
            s--;
            cout << segtree.query(starttime[s], endtime[s]) << "\n";
        }
    }
}
```

## Solve LCA by RMQ
- Use Euler Tour Technique to reduce this problem to RMQ
- You can replace segment tree with sparse table, since we don't need update.
- [https://usaco.guide/CPH.pdf#page=177](https://usaco.guide/CPH.pdf#page=177)
- [CSES - Company Queries II](https://cses.fi/problemset/task/1688)
```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
 
 
void IO(string name = "") {
	cin.tie(0)->sync_with_stdio(0);
	if (name.size()) {
		freopen((name + ".in").c_str(), "r", stdin);
		freopen((name + ".out").c_str(), "w", stdout);
	}
}
 
const ll inf = 2e18;
 
struct SegmentTree {
    int n;
    vector<pair<ll, int>> tree;
    SegmentTree(pair<int,int> inp[], int _n) {
        n = _n;
        while(__builtin_popcount(n)!=1) n++;
        tree.resize(2*n, {inf, inf});
        for(int i = 0; i < n; i++) tree[n+i] = inp[i];
        for(int i = n-1; i >= 1; i--) tree[i] = min(tree[2*i], tree[2*i+1]);
    }
    pair<ll,int> _query2(int l, int r) {
        l += n; r += n;
        pair<ll,int> ans = {inf, inf};
        while(l <= r) {
            if(l%2==1) {
                ans = min(ans, tree[l++]);
            }
            if(r%2==0) ans = min(ans, tree[r--]);
            l /= 2; r /= 2;
        }
        return ans;
    }
    pair<ll,int> query(int l, int r) {
        return _query2(l, r);
    }
};
 
const int maxN = 2e5;
int N, Q;
vector<int> adj[maxN];
int t;
int segtree_id[maxN], depth[maxN];
pair<int, int> segtree_arr[2*maxN-1]; // depth, id
 
void dfs(int u, int p) {
    segtree_id[u] = t;
    segtree_arr[t++] = {depth[u], u};
    for(int v : adj[u]) {
        if(v==p) continue;
        depth[v] = depth[u]+1;
        dfs(v, u);
        segtree_arr[t++] = {depth[u], u};
    }
}

int main() {
    // IO("balancing");
 
    cin >> N >> Q;
    for(int i = 1, a; i <= N-1; i++) {
        cin >> a;
        a--;
        adj[a].push_back(i);
    }
    dfs(0, 1);
    auto segtree = SegmentTree(segtree_arr, 2*N-1);
 
    while(Q--) {
        int a, b;
        cin >> a >> b;
        a--; b--;
        int l = min(segtree_id[a], segtree_id[b]);
        int r = max(segtree_id[a], segtree_id[b]);
        cout << segtree.query(l, r).second+1 << "\n";
    }
}
```
