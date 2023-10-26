[[toc]]

# Eulerian Path and Circuit

## Algorithm
[Video Explain](https://www.youtube.com/watch?v=8MpoO2zA2l4)
- DFS
- Check visited by edges not vertices
- The answer is the reverse of the traversal vertices order by dfs end time (Just like topological sort)

## Undirected Graph
### Existence
- Graph should be connected
- Circuit
    - the degree of all points should be even
- Path
    - for start point and end point: the degree can be odd
    - for all other points: the degree should be even

### Sample Code
- [CSES 1691 Mail Delivery](https://cses.fi/problemset/task/1691)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define ll long long
#define ar array
 
const int maxN = 1e5+5;
int n, m;
int deg[maxN];
set<int> adj[maxN];
vector<int> ans;
 
void dfs(int u) {
    while(adj[u].size()) {
        int v = *adj[u].begin();
        adj[u].erase(v); // remove edges so that we don't traverse the same edge again
        adj[v].erase(u);
        dfs(v);
    }
    ans.push_back(u); // add point to answer by dfs end time
}
 
int main() {
    ios::sync_with_stdio(0); 
    cin.tie(0);
    // freopen("input.txt", "r", stdin); 
    // freopen("output.txt", "w", stdout);
 
    cin >> n >> m;
    for(int i = 0, a, b; i < m; i++) {
        cin >> a >> b;
        a--; b--;
        adj[a].insert(b);
        adj[b].insert(a);
        deg[a]++;
        deg[b]++;
    }

    // check existence by degree 
    for(int i = 0; i < n; i++) {
        if(deg[i]%2!=0) {
            cout << "IMPOSSIBLE";
            return 0;
        }
    }
 
    dfs(0);
    // check existence by connected
    if(ans.size()!=m+1) {
        cout << "IMPOSSIBLE";
        return 0;
    }
    reverse(ans.begin(), ans.end());
    for(int a : ans) cout << a+1 << " ";
}
```

## Directed Graph
### Existence
- Graph should be connected
- Circuit
    - the in degree and out degree of all points should be equal
- Path
    - for start point: the out degree - in degree should be one
    - for end point: the in degree - out degree should be one
    - for all other points: the in degree should be equal to out degree

### Sample Code
- [CSES 1693 Teleporters Path](https://cses.fi/problemset/task/1693)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define ll long long
#define ar array

const int maxN = 1e5+5;
int n, m;
int ind[maxN], outd[maxN];
set<int> adj[maxN];
vector<int> ans;

void dfs(int u) {
    while(adj[u].size()) {
        int v = *adj[u].begin();
        adj[u].erase(v); // remove edge so we will not traverse the same edge again
        dfs(v);
    }
    ans.push_back(u); // add point to answer by dfs end time
}

int main() {
    ios::sync_with_stdio(0); 
    cin.tie(0);
    // freopen("input.txt", "r", stdin); 
    // freopen("output.txt", "w", stdout);

    cin >> n >> m;
    for(int i = 0, a, b; i < m; i++) {
        cin >> a >> b;
        a--; b--;
        adj[a].insert(b);
        outd[a]++;
        ind[b]++;
    }

    // check existence by outdegree indegree
    if(outd[0]-ind[0]!=1 || ind[n-1]-outd[n-1]!=1) {
        cout << "IMPOSSIBLE";
        return 0;
    }
    for(int i = 1; i < n-1; i++) {
        if(outd[i]!=ind[i]) {
            cout << "IMPOSSIBLE";
            return 0;
        }
    }

    dfs(0);
    // check existence by connected
    if(ans.size()!=m+1) {
        cout << "IMPOSSIBLE";
        return 0;
    }
    reverse(ans.begin(), ans.end());
    for(int a : ans) cout << a+1 << " ";
}
```

