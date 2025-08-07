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
    queue<int> qu;
    fill(dist, dist+N, inf);
    dist[0] = 0;
    parent[0] = -1;
    qu.push(0);
    while(qu.size()) {
        // int sz = qu.size();
        // for(int i = 0; i < sz; i++) {
        auto u = qu.front(); qu.pop();
        for(int v : adj[u]) {
            if(dist[v]==inf) {
                dist[v] = dist[u]+1;
                // dist[v] is already correct and fixed at this moment
                parent[v] = u;
                qu.push(v);
            }
        }
        // }
    }
}
```

## Dijkstra
- Single source
- Weight positive
- O(VlogV+E) by Fibonacci Heap,   O((V+E)logE)=O(ElogE) by Binary Heap
- [CSES - Shortest Routes I](https://cses.fi/problemset/task/1671/)

```cpp
const int maxN = 1e5;
int n, m;
vector<pair<int,int>> adj[maxN];
ll dist[maxN];
 
int main() {
    fill(dist, dist+n, 1e18);
    priority_queue<pair<ll,int>, vector<pair<ll,int>>, greater<pair<ll,int>>> pq; // d, u
    dist[0] = 0;
    pq.push({0, 0});
    while(pq.size()) {
        auto [d, u] = pq.top();
        pq.pop();
        // Important!!! if you remove this line, you will get TLE. 
        // Because there are a lot of same nodes will be pushed into pq.
        // You need to ignore those invalid nodes
        if(d > dist[u]) continue; 
        // dist[u] is correct and fixed at this moment
        for(auto [v, w] : adj[u]) {
            if(dist[u] + w < dist[v]) {
                dist[v] = dist[u] + w;
                pq.push({dist[v], v});
            }
        }
    }
}
```

## 0-1 BFS (Very like Dijkstra)
- Single source
- Weight only 0 or 1
- O(V+E)
- https://leetcode.com/problems/minimum-obstacle-removal-to-reach-corner
```cpp
const int maxN = 1e5;
int n, m;
vector<pair<int,int>> adj[maxN];
ll dist[maxN];
 
int main() {
    fill(dist, dist+n, 1e18);
    deque<pair<ll,int>> dq; // d, u
    dist[0] = 0;
    dq.push_back({0, 0});
    while(dq.size()) {
        auto [d, u] = dq.front();
        dq.pop();
        // Unlike dijkstra, this line can be removed and still not get TLE
        // Because same nodes will only be pushed into deque at most 2 times for 0-1 graph
        if(d > dist[u]) continue; 
        // dist[u] is correct and fixed at this moment
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
- Negative weight OK
- No negative cycle
- [CSES - Shortest Routes II](https://cses.fi/problemset/task/1672/)
```cpp
const int maxN = 500;
int n, m, q;
ll d[maxN][maxN];
 
const ll INF = 1e18;
int main() 
    cin >> n >> m >> q;
    fill(&d[0][0], &d[0][0]+maxN*maxN, INF);
    for(int i = 0; i < n; i++) d[i][i] = 0;
    for(int i = 0; i < m; i++) {
        int u, v, w;
        cin >> u >> v >> w;
        u--; v--;
        d[u][v] = min(d[u][v], ll(w));
        d[v][u] = min(d[v][u], ll(w));
    }
 
    // Loop Order Matters!!!! Must loop k first!!!!
    for(int k = 0; k < n; k++) {
        for(int u = 0; u < n; u++) {
            for(int v = 0; v < n; v++) {
                if(d[u][k]==INF || d[k][v]==INF) continue;
                if(d[u][k] + d[k][v] < d[u][v]) { // we can use k as middle point to make dist[i][j] less
                    d[u][v] = d[u][k] + d[k][v];
                }
            }
        }
    }
}
```

## Bellman-Ford
- Single source
- O(V(V+E)) = O(VE)
- Negative weight OK
- Detect negative cycle from start point
    + How to detect if there are any negative cycle in a graph??? Set a super node that connect to all nodes with weight 0
- Speed up (SPFA): Use a queue to store nodes that has been relaxed previous round, then at current round, only run those edges which start from these nodes
    + In competition, this is not required
- [CSES - High Score](https://cses.fi/problemset/task/1673/)
```cpp
const int maxN = 2500;
int n, m;
vector<array<ll, 3>> edges;
ll d[maxN];
const ll INF = 1e18;
 
int main() {
    cin >> n >> m;
    fill(d, d+n, INF);
    for(int i = 0; i < m; i++) {
        int u, v, w;
        cin >> u >> v >> w;
        u--; v--;
        edges.push_back({u, v, -w});
    }
 
    // Bellman-Ford
    d[0] = 0;
    for(int i = 0; i < n-1; i++) {
        for(auto [u, v, w] : edges) {
            if(d[u] == INF) continue;
            if(d[v] > d[u] + w) {
                d[v] = d[u] + w;
            }
        }
    }
 
    // For each point v, check if it is on the cycle.
    for(int i = 0; i < n; i++) {
        for(auto [u, v, w] : edges) {
            if(d[u] == INF) continue;
            if(d[v] > d[u] + w) {
                d[v] = -INF;
            }
        }
    }
    
    if(d[n-1]==-INF) cout << "-1\n";
    else cout << -d[n-1] << "\n";
}
```

## Dijkstra DP
- [CSES - Investigation](https://cses.fi/problemset/task/1202/)

### Pull DP
- Run DAG DP on dijkstra shortest graph (only edges u→v that satisfies dist[u]+w[u,v]==dist[v])
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
            // Important!!! Do not change the order, == must run befor <, because the < case will modify the dist[v]!!!!
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
