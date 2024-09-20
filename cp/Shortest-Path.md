[toc]

# Shortest Path

## BFS
- Single source
- Weight 1
- O(V+E)
- [CSES - Message Route](https://cses.fi/problemset/task/1667)
```cpp
const int inf = 1e9+7;
const int maxN = 1e5;
int N, M;
vector<int> adj[maxN];
int dist[maxN], parent[maxN];
 
int main() {
    fill(dist, dist+N, inf);
    dist[0] = 0;
    parent[0] = -1;
    queue<int> qu;
    qu.push(0);
    while(qu.size()) {
        auto u = qu.front(); qu.pop();
        if(dist[u]==inf) continue;
        for(int v : adj[u]) {
            if(dist[v]!=inf) {
                dist[v] = dist[u]+1;
                parent[v] = u;
                qu.push(v);
            }
        }
    }
}
```

## Dijkstra
- Single source
- Weight positive
- O(VlogV+E) by Fibonacci Heap,   O(VlogV+ElogV)=O(ElogV) by Binary Heap
- [CSES - Shortest Routes I](https://cses.fi/problemset/task/1671/)
```cpp
const int maxN = 1e5;
int n, m;
vector<pair<int,int>> adj[maxN];
ll dist[maxN];
 
int main() {
    fill(dist, dist+n, 1e18);
    priority_queue<pair<ll,ll>, vector<pair<ll,ll>>, greater<pair<ll,ll>>> pq; // d, u
    dist[0] = 0;
    pq.push({0, 0});
    while(pq.size()) {
        auto [d, u] = pq.top();
        pq.pop();
        if(d > dist[u]) continue;
        for(auto [v, w] : adj[u]) {
            if(dist[u] + w < dist[v]) {
                dist[v] = dist[u] + w;
                pq.push({dist[v], v});
            }
        }
    }
}
```

## 0-1 Dijkstra(BFS)
- Single source
- Weight only 0 or 1
- O(V+E)
```cpp
const int maxN = 1e5;
int n, m;
vector<pair<int,int>> adj[maxN];
ll dist[maxN];
 
int main() {
    fill(dist, dist+n, 1e18);
    deque<pair<ll,ll>> dq; // d, u
    dist[0] = 0;
    dq.push_back({0, 0});
    while(dq.size()) {
        auto [d, u] = dq.front();
        dq.pop();
        if(d > dist[u]) continue;
        for(auto [v, w] : adj[u]) {
            if(dist[u] + w < dist[v]) {
                dist[v] = dist[u] + w;
                if(w==1) dq.push_back({dist[v], v});
                else dq.push_front({dist[v], v});
            }
        }
    }
}
```

## Multiple Source Shortest Path
- You want to compute the shortest path of all node to any of the multiple sources
- Algorithm: Just put all source into the queue and run BFS or Dijkstra
- Idea: Add a super node to graph, create weight 1 edges from super node to each source, then run single source shortest path algorithm, then the answer is the result -1

## Floyd-Warshal
- All pair
- O(V^3)
- [CSES - Shortest Routes II](https://cses.fi/problemset/task/1672/)
```cpp
const int maxN = 500;
int n, m, q;
ll d[maxN][maxN];
 
int main() 
    cin >> n >> m >> q;
    fill(&d[0][0], &d[0][0]+maxN*maxN, 1e18);
    for(int i = 0; i < n; i++) d[i][i] = 0;
    for(int i = 0; i < m; i++) {
        int u, v, w;
        cin >> u >> v >> w;
        u--; v--;
        d[u][v] = min(d[u][v], ll(w));
        d[v][u] = min(d[v][u], ll(w));
    }
 
    for(int k = 0; k < n; k++) {
        for(int u = 0; u < n; u++) {
            for(int v = 0; v < n; v++) {
                if(d[u][k] + d[k][v] < d[u][v]) {
                    d[u][v] = d[u][k] + d[k][v];
                }
            }
        }
    }
}
```

## Bellman-Ford
- O(V(V+E)) = O(VE)
- Negative weight OK
- Detect negative cycle
- [CSES - High Score](https://cses.fi/problemset/task/1673/)
```cpp
const int maxN = 2500;
int n, m;
vector<ar<ll, 3>> edges;
ll d[maxN];
 
int main() {
    cin >> n >> m;
    fill(d, d+n, 1e18);
    for(int i = 0; i < m; i++) {
        int u, v, w;
        cin >> u >> v >> w;
        u--; v--;
        edges.push_back({u, v, -w});
    }
 
    d[0] = 0;
    for(int i = 0; i < n; i++) {
        for(auto [u, v, w] : edges) {
            if(d[u] == 1e18) continue;
            if(d[v] > d[u] + w) {
                d[v] = d[u] + w;
            }
        }
    }
 
    for(int i = 0; i < n; i++) {
        for(auto [u, v, w] : edges) {
            if(d[u] == 1e18) continue;
            if(d[v] > d[u] + w) {
                d[v] = -1e18;
            }
        }
    }
    
    if(d[n-1] == -1e18) cout << -1;
    else cout << -d[n-1];
 
}
```

## Dijkstra DP
- [CSES - Investigation](https://cses.fi/problemset/task/1202/)

### Pull DP
- Run DAG DP on dijkstra shortest graph (onlyedges u→v that satisfies dist[u]+w[u,v]==dist[v])
```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
 
const ll maxN = 1e5, inf = 2e18, modN = 1e9+7;
vector<pair<int,int>> adj[maxN];
ll dist[maxN]
 
bool visited[maxN];
ll dp1[maxN], dp2[maxN], dp3[maxN];
void dfs(int u) {
    visited[u] = 1;
    for(auto [v, w] : adj[u]) {
        if(dist[u]+w!=dist[v]) continue;
        if(!visited[v]) dfs(v);
        dp1[u] = (dp1[u]+dp1[v])%modN; 
        if(dp2[v]!=inf)
            dp2[u] = min(dp2[u], dp2[v]+1);
        if(dp3[v]!=-inf) 
            dp3[u] = max(dp3[u], dp3[v]+1);
    }
}
 
int main() {
    int N, M;
    cin >> N >> M;
    for(int i = 0, u,v,w; i < M; i++) {
        cin >> u >> v >> w;
        u--; v--;
        adj[u].push_back({v, w});
    }
 
    // dijkstra
    fill(dist, dist+N, inf);
    dist[0] = 0;
    priority_queue<pair<ll,int>, vector<pair<ll,int>>, greater<pair<ll,int>>> pq;
    pq.push({0, 0});
    while(pq.size()) {
        auto [d, u] = pq.top(); pq.pop();
        if(d>dist[u]) continue;
        for(auto [v, w] : adj[u]) {
            if(dist[u]+w<dist[v]) {
                dist[v] = dist[u]+w;
                pq.push({dist[v], v});
            }
        }
    }
 
    // DP on dijkstra DAG
    dp1[N-1] = 1;
    fill(dp2, dp2+N, inf);
    dp2[N-1] = 0;
    fill(dp3, dp3+N, -inf);
    dp3[N-1] = 0;
    dfs(0);
 
    cout << dist[N-1] << " " << dp1[0] << " " << dp2[0] << " " << dp3[0];
}
```

### Push DP
- Run push DP while doing Dijkstra
- if(dist[u]+w==dist[v]): Original push DP, update dp[v] by dp[u] (update)
- if(dist[u]+w<dist[v]): Original Dijkstra + Init DP value dp[v] by the dp[u] (replace)

```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long

const ll maxN = 1e5, inf = 2e18, modN = 1e9+7;
vector<pair<int,int>> adj[maxN];
ll dist[maxN];
ll dp1[maxN], dp2[maxN], dp3[maxN]

int main() {
    int N, M;
    cin >> N >> M;
    for(int i = 0, u,v,w; i < M; i++) {
        cin >> u >> v >> w;
        u--; v--;
        adj[u].push_back({v, w});
    }

    // init dp
    dp1[0] = 1; // count
    fill(dp2, dp2+N, inf);
    dp2[0] = 0; // min len
    fill(dp3, dp3+N, -inf);
    dp3[0] = 0; // max len

    // dijkstra and push-DP
    fill(dist, dist+N, inf);
    dist[0] = 0;
    priority_queue<pair<ll,int>, vector<pair<ll,int>>, greater<pair<ll,int>>> pq;
    pq.push({0, 0});
    while(pq.size()) {
        auto [d, u] = pq.top(); pq.pop();
        if(d>dist[u]) continue;
        for(auto [v, w] : adj[u]) {
            if(dist[u]+w==dist[v]) {
                dp1[v] = (dp1[v]+dp1[u])%modN;
                dp2[v] = min(dp2[v], dp2[u]+1);
                dp3[v] = max(dp3[v], dp3[u]+1);
            } else if(dist[u]+w<dist[v]) {
                dist[v] = dist[u]+w;
                pq.push({dist[v], v});
                dp1[v] = dp1[u];
                dp2[v] = dp2[u]+1;
                dp3[v] = dp3[u]+1;
            }
        }
    }

    cout << dist[N-1] << " " << dp1[N-1] << " " << dp2[N-1] << " " << dp3[N-1];
}
```

## Johnson
- All pair shortest path
- O(V*(VlogV+E))
- Idea:
    - We want to run Dijkstra on all V, but how to deal with negative weight?
    - Find a way to re-weight our edges such that after re-weight, the weight of each edge becomes positive and the shortest path does not change
- Steps:
    - Add a super node to graph with 0 weight edge to all vertices, then run Bellman Ford to get d(s, v) for all v. Also check if there is any negative cycle in it, if there is, abort
    - Re-weight each edge by: w'(u, v) = w(u, v) + d(s, u) - d(s, v)
    - Run Dijkstra on the re-weighted graph for each v to get d(u, v)
    - d(u, v) = d(u, v) -d(s, u) + d(s, v)
- [https://www.youtube.com/watch?v=EmSmaW-ud6A](https://www.youtube.com/watch?v=EmSmaW-ud6A&list=PLUl4u3cNGP63EdVPNLG3ToM6LaEUuStEY&index=22)
