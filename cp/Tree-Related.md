[[toc]]

# Tree Related

## Find the tree diameter in O(n)
- [CSES - Tree Diameter](https://cses.fi/problemset/task/1131)
- Step
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

## Find the longest path from each node in O(n)
- [CSES - Tree Distances I](https://cses.fi/problemset/task/1132)
- Step
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
