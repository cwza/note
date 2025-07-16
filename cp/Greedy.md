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
