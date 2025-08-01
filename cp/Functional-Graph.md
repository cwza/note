# Functional Graph

## Functional Graph
* Outdegree of all nodes are all 1
* All component has and only has 1 cycle, there are some nodes outside of cycle, and they all points to and only to 1 cycle.
```
      1  2  3  4  5  6  7  8   9  10 11
     [2, 3, 4, 5, 6, 4, 6, 7, 10, 11, 9]
     
     1->2->3->4->5->6<-7<-8   9->10->11
              ^-----|         ^-------|
```
* Find all cycle in functional graph
    + Run topological sort to remove all nodes that outside of cycle
    ```
          1  2  3  4  5  6  7  8   9  10 11   =>   1  2  3  4  5  6  7  8   9  10 11
         [2, 3, 4, 5, 6, 4, 6, 7, 10, 11, 9]      [0, 0, 0, 5, 6, 4, 0, 0, 10, 11, 9]
    ```
    + Traverse the nodes on the new array, when you find you go back to the same node then you find a cycle.
* https://leetcode.com/problems/maximum-employees-to-be-invited-to-a-meeting/description/
``` cpp
class Solution {
public:
    // topological sort
    // https://www.bilibili.com/video/BV12y4y1F79q
    int maximumInvitations(vector<int>& favorite) {
        int n = favorite.size();

        // Topological sort, the nodes outside of cycle will have in-degree 0
        vector<int> in(n);
        for(int i = 0; i < n; i++) in[favorite[i]]++;
        queue<int> qu;
        for(int i = 0; i < n; i++) {
            if(in[i]==0) qu.push(i);
        }
        vector<int> dp(n);
        while(qu.size()) {
            int u = qu.front();
            qu.pop();
            int v = favorite[u];
            in[v]--;
            if(in[v]==0) qu.push(v);
            dp[v] = max(dp[v], dp[u]+1);
        }

        // Traverse cycles
        int ans1 = 0, ans2 = 0;
        for(int i = 0; i < n; i++) {
            if(in[i]==0) continue;
            int cnt = 0; // the number of nodes in this cycle
            int cur = i;
            while(1) {
                cnt++;
                cur = favorite[cur];
                in[cur] = 0; // set in-degree to 0 on cycle nodes to flag that these nodes are traversed
                if(cur==i) break;
            }
            if(cnt>2) ans2 = max(ans2, cnt); 
            if(cnt==2) ans1 += dp[i] + dp[favorite[i]] + 2;
        }
        return max(ans1, ans2);
    }
}
```

## Permutation Graph
* Special Case of Functional Graph
* All component form a cycle, there are no nodes outside of cycle
```
      1  2  3  4  5  6
     [2, 3, 1, 4, 6, 5]

      1->2->3  4--  5->6
      ^-----|  ^-|  ^--|
```
