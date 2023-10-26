# Minimum Spanning Tree
- [CSES - Road Reparation](https://cses.fi/problemset/task/1675/)

## Kruskal's Alg
- O(ElogE)
```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long; 
struct DST {
    vector<int> lk, sz;
    DST(int n) {
        lk.resize(n);
        sz.resize(n, 1);
        for(int i = 0; i < n; i++)  lk[i] = i;
    }
    int find(int a) {
        while(lk[a]!=a) a = lk[a];
        return a;
    }
    bool unite(int a, int b) {
        a = find(a);
        b = find(b);
        if(a==b) return 0;
        if(sz[a]>sz[b]) swap(a, b);
        lk[a] = b;
        sz[b] += sz[a];
        return 1;
    }
};
 
int main() { 
    int N, M;
    cin >> N >> M;
    vector<tuple<int,int,int>> edges(M);
    for(int i = 0, u,v,w; i < M; i++) {
        cin >> u >> v >> w;
        u--; v--;
        edges[i] = {w,u,v};
    }
    sort(edges.begin(), edges.end());
 
    auto dst = DST(N);
    ll ans = 0;
    int cnt = 0;
    for(auto [w, u, v] : edges) {
        if(dst.unite(u, v)) {
            ans += w;
            cnt++;
        }
    }
    if(cnt!=N-1) cout << "IMPOSSIBLE";
    else cout << ans;
}
```

## Prim's Alg
- O(ElogE)
```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
 
const int maxN = 1e5;
vector<pair<int,int>> adj[maxN];
bool visited[maxN];

int main() {
    int N, M;
    cin >> N >> M;
    for(int i = 0, u,v,w; i < M; i++) {
        cin >> u >> v >> w;
        u--; v--;
        adj[u].push_back({w,v});
        adj[v].push_back({w,u});
    }

    priority_queue<pair<int,int>, vector<pair<int,int>>, greater<pair<int,int>>> pq;
    pq.push({0, 0});
    ll ans = 0;
    int cnt = 0;
    while(pq.size()) {
        auto [w, u] = pq.top(); pq.pop();
        if(visited[u]) continue;
        visited[u] = 1;
        ans += w;
        cnt++;
        for(auto [w, v] : adj[u]) {
            if(!visited[v]) pq.push({w, v});
        }
    }
    if(cnt!=N) cout << "IMPOSSIBLE";
    else cout << ans;
}
```
