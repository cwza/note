[[toc]]

# Binary Lifting
- up[u][i] = up[up[u][i-1]][i-1]

## Kth Ancestor of a Tree Node
- [CSES - Company Queries I](https://cses.fi/problemset/task/1687/)
- [https://www.youtube.com/watch?v=oib-XsjFa-M](https://www.youtube.com/watch?v=oib-XsjFa-M)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define ll long long
#define ar array
 
int n, q;
const int maxN = 2e5;
int up[maxN][20], depth[maxN];
vector<int> adj[maxN];
 
void dfs(int u, int p) {
    up[u][0] = p;
    for(int i = 1; i < 20; i++) {
        up[u][i] = up[up[u][i-1]][i-1];
    }
    for(int v : adj[u]) {
        if(v==p) continue;
        depth[v] = depth[u]+1;
        dfs(v, u);
    }
}
 
int main() {
    ios::sync_with_stdio(0); 
    cin.tie(0);
 
    cin >> n >> q;
    for(int i = 1, a; i <= n-1; i++) {
        cin >> a; 
        a--;
        adj[a].push_back(i);
    }
    dfs(0, 0);
 
    while(q--) {
        int x, k;
        cin >> x >> k; 
        x--;
        if(k>depth[x]) cout << -1 << "\n";
        else {
            for(int i = 0; i < 20; i++) {
                if(k&(1<<i)) x = up[x][i];
            }
            cout << x+1 << "\n";
        }
    }
}
```

## LCA
- [CSES - Company Queries II](https://cses.fi/problemset/task/1688/)
- [https://www.youtube.com/watch?v=dOAxrhAUIhA](https://www.youtube.com/watch?v=dOAxrhAUIhA)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define ll long long
#define ar array

int n, q;
const int maxN = 2e5;
vector<int> adj[maxN];
int up[maxN][20], depth[maxN];

void dfs(int u, int p) {
    up[u][0] = p;
    for(int i = 1; i < 20; i++) {
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
    int k = depth[u]-depth[v];
    for(int i = 0; i < 20; i++) {
        if(k&(1<<i)) u = up[u][i];
    }
    if(u==v) return u;
    for(int i = 19; i>=0; i--) {
        if(up[u][i]!=up[v][i]) {
            u = up[u][i];
            v = up[v][i];
        }
    }
    return up[u][0];
}

int main() {
    ios::sync_with_stdio(0); 
    cin.tie(0);

    cin >> n >> q;
    for(int i = 1, a; i <= n-1; i++) {
        cin >> a; 
        a--;
        adj[a].push_back(i);
    }
    dfs(0, 0);

    while(q--) {
        int a, b;
        cin >> a >> b;
        a--; b--;
        cout << lca(a, b)+1 << "\n"; 
    }
}
```

## Find dist(u, v) on Tree
- [CSES - Distance Queries](https://cses.fi/problemset/task/1135/)
- dist(u, v) = depth(u)+depth(v)-2*depth[lca(u, v)]
```cpp
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
    dfs(0, 0);

    while(q--) {
        int a, b;
        cin >> a >> b;
        a--; b--;
        cout << depth[a]+depth[b]-2*depth[lca(a,b)] << "\n";
    }
}
```
