# Minimum Spanning Tree
* Un-directive weighted graph
* Find a tree that has minimized sum of edges, and the graph still connected
* MST is also a tree that minimize the maximum edge weight
* N nodes graph:  minimum spanning tree N-1 edges
    + If you find your tree edges less than n-1 after run the MST algorithm, this means the original graph is not connected
* https://www.luogu.com.cn/problem/P3366  or https://cses.fi/problemset/task/1675/

## Kruskal's Algorithm
* O(ElogE) + O(V)
* Algorithm:
    + Sort edge list, process edges from small to large
    + Use DST to check cycle, do not pick those edges which may form a cycle
* Greedy point: pick the minimum edge from all edges
```cpp
#include <bits/stdc++.h>
using namespace std;
#ifdef DEBUG
    #include "./tool/debug.hpp"
#else
    #define dbg(...)
    #define dbgarr(a)
#endif
using ll = long long;

int main() {
    ios_base::sync_with_stdio(0);
    cin.tie(0);

    int n, m;
    cin >> n >> m;
    vector<tuple<int, int, int>> edges(m); 
    for(int i = 0, u, v, w; i < m; i++) {
        cin >> u >> v >> w;
        edges[i] = {w, u, v};
    }
    sort(edges.begin(), edges.end());

    vector<int> lk(n+1);
    iota(lk.begin(), lk.end(), 0);
    auto find = [&](auto &&self, int x) -> int {
        if(x!=lk[x]) lk[x] = self(self, lk[x]);
        return lk[x];
    };
    auto unite = [&](int x, int y) -> bool {
        x = find(find, x);
        y = find(find, y);
        if(x!=y) {
            lk[x] = y;
            return 1;
        }
        return 0;
    };

    int cnt = 0, ans = 0;
    for(auto [w, u, v] : edges) {
        if(unite(u, v)) {
            cnt++;
            ans += w;
        }
    }
    if(cnt!=n-1) cout << "orz\n";
    else cout << ans << "\n";
}
```

## Prim's Algorithm
* O(ElogE) + O(V), Can be optimized to O(ElogV) + O(V), but in competetion this is not required
* Algorithm:
    + Use minimum priority queue to maintain the edges of all processed nodes
    + Use set to check whether node is processed or not
    + First start from any node, and push its edges to priority queue, add this node to proccessed set
    + Repeatly do following: pull the minimum edge from priority queue, if it is not in proccessed set,  add its edges to priority queue, and add this node to proccessed set
* This algorithm looks very like Dijkstra
* Greedy point: Pick the minimum edge from processed node's edges
```cpp
#include <bits/stdc++.h>
using namespace std;
#ifdef DEBUG
    #include "./tool/debug.hpp"
#else
    #define dbg(...)
    #define dbgarr(a)
#endif
using ll = long long;

int main() {
    ios_base::sync_with_stdio(0);
    cin.tie(0);

    int n, m;
    cin >> n >> m;

    vector<vector<pair<int,int>>> adj(n+1, vector<pair<int, int>>());
    for(int i = 0, u, v, w; i < m; i++) {
        cin >> u >> v >> w;
        adj[u].push_back({w, v});
        adj[v].push_back({w, u});
    }

    priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> pq;
    vector<int> vis(n+1);
    for(auto [w, v] : adj[1]) pq.push({w, v});
    vis[1] = 1;
    int cnt = 1, ans = 0;
    while(pq.size()) {
        auto [wu, u] = pq.top();
        pq.pop();
        if(vis[u]) continue;
        vis[u] = 1;
        cnt++;
        ans += wu;
        for(auto [wv, v] : adj[u]) {
            if(!vis[v]) pq.push({wv, v});
        }
    }
    if(cnt!=n) cout << "orz\n";
    else cout << ans << "\n";
}
```
