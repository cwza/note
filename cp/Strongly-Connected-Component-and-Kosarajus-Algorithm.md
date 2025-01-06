# Strongly Connected Component and Kosaraju's Algorithm
* Run dfs on original graph, push nodes into stack by dfs end time order
* Reverse the graph to get rgraph
* Run dfs on rgraph by the stack poped order
* Note that in dfs2 Kosaraju's algorithm will proccess the nodes(grouped) by topological sort!!! This fact is useful if you want to run some DP on compressed graph
* https://cses.fi/problemset/task/1682/
``` cpp
#include <bits/stdc++.h>
using namespace std;
#ifdef DEBUG
    #include "./tool/debug.hpp"
#else
    #define dbg(...)
    #define dbgarr(a)
#endif
using ll = long long;

struct SCC {
    int n;
    vector<vector<int>> adj, r_adj;
    vector<int> group, group_ids;
    vector<vector<int>> compressed_adj;
    SCC(vector<vector<int>> &_adj): adj(_adj) {
        n = adj.size();
        r_adj.resize(n);
        for(int u = 0; u < n; u++) {
            for(int v : adj[u]) {
                r_adj[v].push_back(u);
            }
        }
    }
    void run() {
        vector<int> vis(n);
        vector<int> st;
        auto dfs = [&](auto &&self, int u) -> void {
            vis[u] = 1;
            for(int v : adj[u]) {
                if(!vis[v]) self(self, v);
            }
            st.push_back(u);
        };
        for(int u = 0; u < n; u++) {
            if(!vis[u]) dfs(dfs, u);
        }
        reverse(st.begin(), st.end());
        vis.clear(); vis.resize(n);
        group.resize(n);
        auto dfs2 = [&](auto &&self, int u, int group_num) -> void {
            vis[u] = 1;
            group[u] = group_num;
            for(int v : r_adj[u]) {
                if(!vis[v]) self(self, v, group_num);
            }
        };
        for(int u : st) {
            if(!vis[u]) {
                dfs2(dfs2, u, u);
                group_ids.push_back(u);
            }
        }
    }
    void compress() {
        compressed_adj.resize(n);
        for(int u = 0; u < n; u++) {
            for(int v : adj[u]) {
                if(group[u]==group[v]) continue;
                compressed_adj[group[u]].push_back(group[v]);
            }
        }
        for(int u = 0; u < n; u++) {
            sort(compressed_adj[u].begin(), compressed_adj[u].end());
            compressed_adj[u].resize(unique(compressed_adj[u].begin(), compressed_adj[u].end())-compressed_adj[u].begin());
        }
    }
};


int main() {
    int n, m;
    cin >> n >> m;
    vector<vector<int>> adj(n);
    for(int i = 0, u, v; i < m; i++) {
        cin >> u >> v;
        u--; v--;
        adj[u].push_back(v);
    }

    auto scc = SCC(adj);
    scc.run();

    assert(scc.group_ids.size()!=0);
    if(scc.group_ids.size()==1) cout << "YES\n";
    else {
        cout << "NO\n";
        cout << scc.group_ids[1]+1 << " " << scc.group_ids[0]+1 << "\n";
    }
}
```
