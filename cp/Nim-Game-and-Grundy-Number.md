# Nim Game and Grundy Number
* https://cp-algorithms.com/game_theory/sprague-grundy-nim.html
* https://www.youtube.com/watch?v=zqWjEEFpKQE

## Basic Nim Game
* N piles, each pile contains x1, x2, ..., xn balls, 2 players play this game, for each one need to remove at least one ball on one pile, who get the last ball wins, if theyi both act optimally, who wins?
* Lose State: who at this state will lose <=> x1 xor x2 xor ... xor xn = 0
``` cpp
#include <bits/stdc++.h>
using namespace std;
int main() {
    int n;
    cin >> n;
    vector<int> a(n);
    for(int i = 0; i < n; i++) cin >> a[i]; 
    int ans = 0;
    for(int i = 0; i < n; i++) {
        ans ^= a[i];
    }
    if(ans==0) cout << "Bob\n";
    else cout << "Alice\n";
}
```

## Constraint Nim Game Solved by Grundy Number
* Solve Process:
    + Brute force to get some grundy numbers
    + Observe the pattern
    + Use pattern to compute all grundy numbers
    + Run basic nim game on these grundy numbers
* grundy[x] = MEX(grundy[nxt1], grundy[nxt2], ...), nxti is the state which x can go to
    + Mex(1, 2) = 0, Mex(0, 2, 3) = 1, Mex(0, 1, 2) = 3
* Example Problem: Same as nim game but they can only remove at most k balls from one pile
* Other problem: https://atcoder.jp/contests/abc368/tasks/abc368_f
``` cpp
#include <bits/stdc++.h>
using namespace std;
#ifdef DEBUG
    #include "debug.hpp"
#else
    #define dbg(...)
#endif
using ll = long long;

// brute force first 100 values to observe that the pattern is just x%(k+1)
void bf(int k) {
    auto mex = [](vector<int> &vs) -> int {
        sort(vs.begin(), vs.end());
        vs.resize(unique(vs.begin(), vs.end())-vs.begin());
        for(int i = 0; i < (int)vs.size(); i++) {
            if(i!=vs[i]) return i;
        }
        return vs.size();
    };
    int maxA = 100;
    vector<int> grundy(maxA+1);
    grundy[0] = 0;
    for(int i = 1; i <= maxA; i++) {
        vector<int> nxts;
        for(int j = 1; j <= k; j++) {
            if(i-j>=0) nxts.push_back(grundy[i-j]);
        }
        grundy[i] = mex(nxts);
        dbg(i, grundy[i]);
    }
}

const int maxA = 1e5;
vector<int> grundy;

void pre(int k) {
    grundy.resize(maxA+1);
    grundy[0] = 0;
    for(int i = 1; i <= maxA; i++) {
        grundy[i] = i%(k+1);
    }
}

void solve() {
    int n, k;
    cin >> n >> k;
    pre(k);
    vector<int> a(n);
    for(int i = 0; i < n; i++) cin >> a[i];

    int ans = 0;
    for(int i = 0; i < n; i++) {
        ans ^= grundy[a[i]];
    }
    if(ans==0) cout << "Bob\n";
    else cout << "Alice\n";
}
 
bool multi = 0;
int main() {
    ios_base::sync_with_stdio(0);
    cin.tie(0);
 
    // bf(4);
    int t = 1;
    if (multi)
        cin >> t;
    for (int i = 0; i < t; i++) {
        solve();
    }
}
```
