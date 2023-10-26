# Topological Sort
- [CSES - Course Schedule](https://cses.fi/problemset/task/1679/)

## By DFS
- reverse of dfs end_time
```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long
 
const int maxN = 1e5;
int N, M;
vector<int> adj[maxN];
int visited[maxN];
vector<int> order;
 
void dfs(int u) {
    visited[u] = 1;
    for(int v : adj[u]) {
        if(visited[v]==1) {
            cout << "IMPOSSIBLE";
            exit(0);
        }
        if(!visited[v]) dfs(v);
    }
    order.push_back(u);
    visited[u] = 2;
}
 
int main() {
    cin >> N >> M;
    for(int i = 0, a, b; i < M; i++) {
        cin >> a >> b;
        a--; b--;
        adj[a].push_back(b);
    }
 
    for(int i = 0; i < N; i++) {
        if(!visited[i]) dfs(i);
    }
 
    reverse(order.begin(), order.end());
    for(int a : order) cout << a+1 << " ";
}
```

## By BFS
- Idea: Add indegree 0 nodes into answer, then remove them from graph, repeat until no indegree 0 nodes
    - Maintain a queue that always have indegree 0 nodes during BFS
```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;

const int maxN = 1e5;
int N, M;
vector<int> adj[maxN];
int indegree[maxN];
vector<int> order;
 
 
int main() {
    cin >> N >> M;
    for(int i = 0, a, b; i < M; i++) {
        cin >> a >> b;
        a--; b--;
        adj[a].push_back(b);
        indegree[b]++;
    }
 
    queue<int> qu;
    for(int i = 0; i < N; i++) {
        if(indegree[i]==0) qu.push(i);
    }
    while(qu.size()) {
        int u = qu.front(); qu.pop();
        order.push_back(u);
        for(int v : adj[u]) {
            indegree[v]--;
            if(indegree[v]==0) qu.push(v);
        }
    }
 
    if(order.size()!=N) {
        cout << "IMPOSSIBLE";
    } else {
        for(int a : order) cout << a+1 << " ";
    }
}
```
