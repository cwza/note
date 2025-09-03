# Greedy

## Question 1
We have an integer array B = [B1, B2, ..., Bn] with us. Each Bi is >= 0.
In one move, we can subtract 1 from at most M different indices of B, and we’d like to find the minimum number of moves to reduce all elements to 0.
* The solution is max( mx, ceil(sum/M) ), where mx = max(Bi), sum = sum(Bi)
* O(N)
* For this kind of problems, always focus on two key values: average and max. Then try to consider when max<=average ... otherwise ....

## Question 2
Given 2 string s and t, check if t is a subsequence of s
* Use 2 pointer: if s[i]==t[j] then i++, j++ else j++
* O(N)
``` cpp
#include <bits/stdc++.h>
using namespace std;
int main() {
    string s, t;
    cin >> s >> t;
    int n = s.size();
    int m = t.size();
    if(m>n) {
        cout << "NO\n";
        return;
    }
    int j = 0;
    for(int i = 0; i < n && j < m; i++) {
        if(s[i]==t[j]) j++;
    }
    if(j==m) cout << "YES\n";
    else cout << "NO\n";
}
```

## Question 3
From 1 to N, pick K integers, which sum to target
* O(K)
```
ex: From 1 to 30, pick 5 integers, sum to 100

         1 2 3 4 5  .......... 26 27 28 29 30

1. if target < 1+2+3+4+5 or target > 26+27+28+29+30 then we have no answer
2. pick [1, 2, 3, 4, 5], now we have 100-(1+2+3+4+5)=85 left
3. change 5 to 30 [1, 2, 3, 4, 30], now we have 85-(30-5)=60 left
4. change 4 to 29 [1, 2, 3, 29, 30], now we have 60-(29-4)=35 left
5. change 3 to 28 [1, 2, 28, 29, 30], now we have 60-(28-3)=10 left
6. change 2 to 12 [1, 12, 28, 29, 30], now we have 10-10=0 left
7. each time from right to left, we try to add n-k(25) to the right most number, when left<=n-k then we add left to it and finish
```
``` cpp
vector<int> solve(int n, int k, ll target) {
    ll sm_left = 0;
    for(int i = 1; i <= k; i++) sm_left += i;
    ll sm_right = 0;
    for(int i = n; i > n-k; i--) sm_right += i;
    if(target<sm_left || target>sm_right) return {};

    vector<int> ans(k);
    iota(ans.begin(), ans.end(), 1);
    ll need = target-sm_left;
    int range = n-k;
    for(int i = k-1; i >= 0; i--) {
        if(need<=range) {
            ans[i] += need;
            break;
        } else {
            ans[i] += range;
            need -= range;
        }
    }
    return ans;
}

int main() {
    ios_base::sync_with_stdio(0);
    cin.tie(0);
    
    auto ans = solve(30, 5, 100);
    dbg(ans); // [1, 12, 28, 29, 30]
}
```
