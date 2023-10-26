# Tarjan’s Algorithm

## Find SCC of directed graph
- [Video Explain](https://www.youtube.com/watch?v=TyWtx7q2D7Y)
- [CSES 1682 Flight Routes Check](https://cses.fi/problemset/task/1682/)
```cpp
#include <bits/stdc++.h>
using namespace std;
#define ll long long
#define ar array

const int maxN = 1e5;
int n, m;
vector<int> adj[maxN];
bool visited[maxN];

int disc[maxN], low[maxN];
int t = 0;
stack<int> st;
bool instack[maxN];
int who[maxN];
vector<int> roots;

void dfs(int u) {
    visited[u] = 1;
    disc[u] = t;
    low[u] = t;
    t++;
    st.push(u);
    instack[u] = 1;
    for(int v : adj[u]) {
        if(!visited[v]) {
            dfs(v);
            low[u] = min(low[u], low[v]);
        } else if(instack[v]) {
            low[u] = min(low[u], disc[v]);
        }
    }
    if(low[u]==disc[u]) {
        roots.push_back(u);
        int x;
        do {
            x = st.top();
            who[x] = u;
            instack[x] = 0;
            st.pop();
        } while(x!=u);
    }
}

int main() {
    ios::sync_with_stdio(0); 
    cin.tie(0);
    // freopen("input.txt", "r", stdin); 
    // freopen("output.txt", "w", stdout);

    cin >> n >> m;
    fill(who, who+n, -1);
    for(int i = 0, a, b; i < m; i++) {
        cin >> a >> b;
        a--; b--;
        adj[a].push_back(b);
    }
 
    for(int i = 0; i < n; i++) {
        if(!visited[i]) {
            dfs(i);
        }
    }

    // for(int i = 0; i < n; i++) cout << disc[i] << " ";
    // cout << endl;
    // for(int i = 0; i < n; i++) cout << low[i] << " ";
    // cout << endl;
    // for(int i = 0; i < n; i++) cout << who[i] << " ";
    // cout << endl;
    // for(auto root : roots) cout << root << " ";
    // cout << endl;
    
    if(roots.size()==1) {
        cout << "YES";
        return 0;
    }
    cout << "NO\n";
    cout << roots[0]+1 << " " << roots[1]+1;
}
```

## Find bridge on undirected graph
- [Critical Connections in a Network - LeetCode](https://leetcode.com/problems/critical-connections-in-a-network/description/)
- Key:
```cpp
#include <bits/stdc++.h>
using namespace std;
#define ll long long
#define ar array

const int maxN = 1e5;
class Solution {
public:
    int n;
    vector<int> adj[maxN];
    bool visited[maxN];
    int disc[maxN], low[maxN], who[maxN];
    int t;
    vector<vector<int>> ans;
    void dfs(int u, int p) {
        disc[u] = t;
        low[u] = t;
        t++;
        visited[u] = 1;
        for(int v : adj[u]) {
            if(v==p) continue;
            if(!visited[v]) {
                dfs(v, u);
                low[u] = min(low[u], low[v]);
                if(disc[u]<low[v]) {
                    ans.push_back({u, v});
                }
            } else {
                low[u] = min(low[u], disc[v]);
            }
        }
    }
    vector<vector<int>> criticalConnections(int nn, vector<vector<int>>& connections) {
        n = nn;
        fill(who, who+n, -1);
        for(auto connection : connections) {
            int u = connection[0];
            int v = connection[1];
            adj[u].push_back(v);
            adj[v].push_back(u);
        }
        for(int u = 0; u < n; u++) {
            if(!visited[u]) dfs(u, -1);
        }
        return ans;
    }
};

int main() {
    ios::sync_with_stdio(0); 
    cin.tie(0);
    // freopen("input.txt", "r", stdin); 
    // freopen("output.txt", "w", stdout);

    int n = 4;
    vector<vector<int>> connections = {
        {0,1},{1,2},{2,0},{1,3}
    };
    auto ans = Solution().criticalConnections(n, connections);
    for(auto a : ans) {
        for(auto b : a) cout << b << ",";
        cout << endl;
    }
}
```
