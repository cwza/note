# DFS and BFS

## DFS (Recursive)
```cpp
int n;
const int maxN = 1e5;
vector<int> adj[maxN];
bool visited[maxN];
int parent[maxN];

void dfs(int u) {
    visited[u] = 1;
    for(int v : adj[u]) {
        if(!visited[u]) {
            parent[v] = u;
            dfs(v);
		}
    }
}

int main() {
    ios::sync_with_stdio(0); 
    cin.tie(0);
    
    for(int u = 0; u < n; u++) {
        if(!visited[u]) {
            dfs(u);
        }
    }
}
```

## DFS (Stack)
```cpp
int n;
const int maxN = 1e5;
vector<int> adj[maxN];
bool visited[maxN];
int parent[maxN];

void dfs(int u) {
    stack<int> st;
    st.push(u);
    while(st.size()) {
        u = st.top();
        st.pop();
        visited[u] = 1;
        for(int v : adj[u]) {
            if(!visited[u]) {
                st.push(v);
                parent[v] = u;
            }
        }
    }
}

int main() {
    ios::sync_with_stdio(0); 
    cin.tie(0);
    
    for(int u = 0; u < n; u++) {
        if(!visited[u]) {
            dfs(u);
        }
    }
}
```

## BFS
```cpp
int n;
const int maxN = 1e5;
vector<int> adj[maxN];
bool visited[maxN];
int parent[maxN];

int main() {
    ios::sync_with_stdio(0); 
    cin.tie(0);
    
    queue<int> q;
    visited[0] = 1;
    parent[0] = -1;
    q.push(0);

    while(q.size()) {
        // int sz = q.size();
        // for(int i = 0; i < sz; i++) {
        int u = q.front(); q.pop();
        for(int v : adj[u]) {
            if(!visited[v]) {
                visited[v] = 1;
                parent[v] = u;
                q.push(v);
            }
        }
        // }
    }
}
```
