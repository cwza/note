[[toc]]

# Tree Related

## Find the tree diameter in O(n)
- [CSES - Tree Diameter](https://cses.fi/problemset/task/1131)
### Solution1
- Root any
- run dfs to find the farest node from root, we denote it as farest1
- run dfs to find the farest node from farest1, we denote it as farest2
- The answer is the distance between farest1 and farest 2
```cpp
const int maxN = 2e5;
vector<int> adj[maxN];
 
void dfs(int u, int p, vector<int> &dist) {
    for(int v : adj[u]) {
        if(v==p) continue;
        dist[v] = dist[u]+1;
        dfs(v, u, dist);
    }
}
 
int main() {
    ios_base::sync_with_stdio(0);
		cin.tie(0);
 
    int n;
    cin >> n;
    for(int i = 0, a, b; i < n-1; i++) {
        cin >> a >> b;
        a--; b--;
        adj[a].push_back(b);
        adj[b].push_back(a);
    }
    vector<int> dist(n);
    dfs(0, -1, dist);
    int farest1 = max_element(dist.begin(), dist.end())-dist.begin();
    vector<int> dist1(n);
    dfs(farest1, -1, dist1);
    int farest2 = max_element(dist1.begin(), dist1.end())-dist1.begin();
    cout << dist1[farest2];
}
```
### Solution2
- Root any
- For each node u, within its subtree, find the 1st largest length from it to leaf(height1[u]), and also find the 2nd largest length from it to leaf(height2[u])
    + height2[u] = max(height2[u], height1[v] + 1)
    + if(height2[u]>height1[u]) swap(height2[u], height1[u])
- Take the max of height1[u]+height2[u] through each node, this is the answer
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


void solve() {
    int n;
    cin >> n;
    vector<vector<int>> adj(n);
    for(int i = 0, u, v; i < n-1; i++) {
        cin >> u >> v;
        u--; v--;
        adj[u].push_back(v);
        adj[v].push_back(u);
    }
    int ans = 0;
    vector<int> height1(n), height2(n);
    auto dfs = [&](auto &self, int u, int p) -> void {
        for(int v : adj[u]) {
            if(v==p) continue;
            self(self, v, u);
            height2[u] = max(height2[u], height1[v]+1);
            if(height2[u]>height1[u]) swap(height2[u], height1[u]);
        }
        ans = max(ans, height1[u]+height2[u]);
    };
    dfs(dfs, 0, -1);
    cout << ans << "\n";
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

## Find the longest path from each node in O(n)
- [CSES - Tree Distances I](https://cses.fi/problemset/task/1132)
### Solution1
- Root any
- run dfs to find the farest node from root, we denote it as farest1
- run dfs to find the farest node from farest1, we denote it as farest2
- The answer is for each node v, max(dist(farest1, v), dist(farest2, v))
```cpp
const int maxN = 2e5;
vector<int> adj[maxN];
 
void dfs(int u, int p, vector<int> &dist) {
    for(int v : adj[u]) {
        if(v==p) continue;
        dist[v] = dist[u]+1;
        dfs(v, u, dist);
    }
}
 
int main() {
    ios_base::sync_with_stdio(0);
		cin.tie(0);
 
    int n;
    cin >> n;
    for(int i = 0, a, b; i < n-1; i++) {
        cin >> a >> b;
        a--; b--;
        adj[a].push_back(b);
        adj[b].push_back(a);
    }
    vector<int> dist(n);
    dfs(0, -1, dist);
    int farest1 = max_element(dist.begin(), dist.end())-dist.begin();
    vector<int> dist1(n);
    dfs(farest1, -1, dist1);
    int farest2 = max_element(dist1.begin(), dist1.end())-dist1.begin();
    vector<int> dist2(n);
    dfs(farest2, -1, dist2);
    for(int i = 0; i < n; i++) cout << max(dist1[i], dist2[i]) << " ";
}
```
### Solution2
- Root any
- We need to consider two paths. Path1 is from u down to leaf. Path2 is from u go up to its parent p.
- dfs1: For each node u, within its subtree, find the 1st and 2nd largest length from it to leaf (down1[u], down2[u])
    + please refers to `find the tree diameter section` above
- dfs2: For each node u, go through its parent, find the largest length from it go through its parent and to leaf (up[u])
    + up[u] = max(up[u], up[p]+1);
    + if(down1[u]+1!=down1[p]) up[u] = max(up[u], down1[p]+1); `// use down1[p] if this down1[p] is not the path that go through u`
    + else up[u] = max(up[u], down2[p]+1); `// else use down2[p]`
- For each node u, take the max of up[u], down1[u] and this is answer
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


void solve() {
    int n;
    cin >> n;
    vector<vector<int>> adj(n);
    for(int i = 0, u, v; i < n-1; i++) {
        cin >> u >> v;
        u--; v--;
        adj[u].push_back(v);
        adj[v].push_back(u);
    }
    vector<int> down1(n), down2(n);
    auto dfs = [&](auto &self, int u, int p) -> void {
        for(int v : adj[u]) {
            if(v==p) continue;
            self(self, v, u);
            down2[u] = max(down2[u], down1[v]+1);
            if(down2[u]>down1[u]) swap(down2[u], down1[u]);
        }
    };
    dfs(dfs, 0, -1);
    vector<int> up(n);
    auto dfs2 = [&](auto &self, int u, int p) -> void {
        if(p!=-1) {
            up[u] = max(up[u], up[p]+1);
            if(down1[u]+1!=down1[p]) up[u] = max(up[u], down1[p]+1);
            else up[u] = max(up[u], down2[p]+1);
        }
        for(int v : adj[u]) {
            if(v==p) continue;
            self(self, v, u);
        }
    };
    dfs2(dfs2, 0, -1);
    for(int i = 0; i < n; i++) cout << max(up[i], down1[i]) << " ";
    cout << "\n";
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

## Query dist(u, v) for all u, v in O(nlogn) by LCA
- [CSES - Distance Queries](https://cses.fi/problemset/task/1135)
- Step
    - Precompute depth(u) for all u [O(n)]
    - Precompute LCA(u, v) for all u, v [O(nlogn)] by binary lifting
    - dist(u, v) = depth(u) + depth(v) - 2*(depth(lca(u, v))) [O(logn)]
```cpp
const int maxN = 2e5, maxLogN = 20;
vector<int> adj[maxN];
int depth[maxN];
int up[maxN][maxLogN];

void dfs(int u, int p) {
    up[u][0] = p;
    for(int i = 1; i < maxLogN; i++) {
        up[u][i] = up[up[u][i-1]][i-1];
    }
    for(int v : adj[u]) {
        if(v==p) continue;
        depth[v] = depth[u]+1;
        dfs(v, u);
    }
}

int lca(int u, int v) {
    if(depth[u]<depth[v]) swap(u, v);
    for(int i = 0; i < maxLogN; i++) {
        if((depth[u]-depth[v])&(1<<i)) u = up[u][i];
    }
    if(u==v) return u;
    for(int i = maxLogN-1; i>=0; i--) {
        if(up[u][i]==up[v][i]) continue;
        u = up[u][i];
        v = up[v][i];
    }
    return up[u][0];
}

int dist(int u, int v) {
    return depth[u]+depth[v]-2*depth[lca(u,v)];
}

int main() {
    ios::sync_with_stdio(0); 
    cin.tie(0);
 
    cin >> n >> q;
    for(int i = 1, a, b; i <= n-1; i++) {
        cin >> a >> b; 
        a--; b--;
        adj[a].push_back(b);
        adj[b].push_back(a);
    }
    dfs(0, 0); // not (0, -1)!!! 
 
    while(q--) {
        int a, b;
        cin >> a >> b;
        a--; b--;
        cout << dist(a, b) << "\n";
    }
}
```

## Find Centroid of Tree
* A centroid of a tree is defined as a node such that when the tree is rooted at it, no other nodes have a subtree of size greater than n/2
* Properties:
    + The number of centroids is always >= 1 and <= 2
    + If there are 2 centroids, they must be adjacent to each other, and they must form a subtree that has exactly n/2 nodes.
    + If C is the centroid of a tree, the sum of distances of all other verticies to C is minimum.
    + The diameter of a tree is always contains centroids
* run dfs from root, if node has any subtree that size greater than n/2 dfs such subtree, else it is the centroid
* Problems
    + https://atcoder.jp/contests/abc362/tasks/abc362_f
``` cpp
int n;
const int maxN = 2e5;
vector<int> adj[maxN];
int cnt[maxN];
int centroid;

void dfs(int u, int p) {
    cnt[u] = 1;
    for(int v : adj[u]) {
        if(v==p) continue;
        dfs(v, u);
        cnt[u] += cnt[v];
    }
}

void dfs2(int u, int p) {
    bool ok = 1;
    for(int v : adj[u]) {
        if(v==p) continue;
        if(cnt[v]>n/2) {
            ok = 0;
            dfs2(v, u);
        }
    }
    if(ok) {
        centroid = u;
    }
}
int main() {
    dfs(0, -1); // find subtree size for each node
    dfs2(0, -1); // find centroid
    dbg(centroid);
}
```

## Point Update Range Sum Query on Tree
* https://cses.fi/problemset/task/1137/
* Key:
    + Use dfs start time as index in segment tree(or fenwick tree)
    + dfn[i] : dfs start time of i
    + sz[i]: subtree size of i
    + range sum of subtree i: range sum of [dfn[i], dfn[i]+sz[i]-1] in segment tree
``` cpp
/*
Idea from CPH
    0
   . .
  1   2
     . .
    3   4
 
            0 1 2 3 4 (original index)
dfn         0 1 2 3 4 (index in segment tree)
sz          5 1 3 1 1
value       4 2 5 2 1

sum of subtree at i = sum in range [dfn[i], dfn[i]+sz[i]-1]
*/


void solve() {
    int n, q;
    cin >> n >> q;
    vector<int> inp(n);
    for(int i = 0; i < n; i++) cin >> inp[i];
    vector<vector<int>> adj(n);
    for(int i = 0, u, v; i < n-1; i++) {
        cin >> u >> v;
        u--; v--;
        adj[u].push_back(v);
        adj[v].push_back(u);
    }

    vector<int> sz(n), dfn(n), nfd(n);
    int t = 0;
    auto dfs = [&](auto &self, int u, int p) -> void {
        sz[u] = 1;
        dfn[u] = t;
        nfd[t] = u;
        t++;
        for(int v : adj[u]) {
            if(v!=p) {
                self(self, v, u);
                sz[u] += sz[v];
            }
        }
    };
    dfs(dfs, 0, -1);
    dbg(sz);
    dbg(dfn);

    atcoder::fenwick_tree<long long> fw(n);
    for(int i = 0; i < n; i++) {
        fw.add(dfn[i], inp[i]);
    }

    while(q--) {
        int mode;
        cin >> mode;
        if(mode==1) {
            int s, x;
            cin >> s >> x;
            s--;
            fw.add(dfn[s], x-inp[s]);
            inp[s] = x;
        } else {
            int s;
            cin >> s;
            s--;
            cout << fw.sum(dfn[s], dfn[s]+sz[s]) << "\n";
        }
    }
}
```
