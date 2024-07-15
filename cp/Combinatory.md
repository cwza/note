[[toc]]

# Combinatory

## N choose K, O(n)
- [D - Knight (atcoder.jp)](https://atcoder.jp/contests/abc145/tasks/abc145_d)
```cpp
long long choose(long long n, long long k) {
    if(n<0 || k<0 || n<k) return 0;
    if(k==0) return 1;
    long long res = 1;
    for(int i = 1; i <= k; i++) {
        res *= n-k+i;
        res /= i;
    }
    return res;
}

long long modchoose(long long n, long long k, long long modN) {
    if(n<0 || k<0 || n<k) return 0;
    if(k==0) return 1;
    long long res = 1;
    for(int i = 1; i <= k; i++) {
        res = res * (n-k+i) % modN;
        res = res * modinv(i, modN) % modN;
    }
    return res%modN;
}
```

## Precompute 1\~N! and mod_inv(1\~N!) in O(N), then query N choose K in O(1)
- [CSES - Binomial Coefficients](https://cses.fi/problemset/task/1079)
- fact[i] = fact[i-1]*i
- fact_inv[i] = fact[i+1]*(i+1)
```cpp
const int maxN = 1e6;
const ll modN = 1e9+7;
ll fact[maxN+1], fact_inv[maxN+1];

ll fpow(ll a, ll b, ll modN) {
    ll res = 1;
    while(b) {
        if(b%2==1) res = res*a%modN;
        a = a*a%modN;
        b /= 2;
    }
    return res;
}
 
ll mod_inv(ll a) {
    return fpow(a, modN-2, modN);
}

ll choose(ll n, ll k) {
    if(n<0 || k<0 || n<k) return 0;
    if(k==0) return 1;
    return fact[n]*fact_inv[k]%modN*fact_inv[n-k]%modN;
}

int main() {
    // precompute
    fact[0] = 1;
    for(int i = 1; i <= maxN; i++) {
        fact[i] = i*fact[i-1]%modN;
    }
    fact_inv[maxN] = mod_inv(fact[maxN]);
    for(int i = maxN-1; i >= 0; i--) {
        fact_inv[i] = fact_inv[i+1]*(i+1)%modN;
    }
		
    int n;
    cin >> n;
    while(n--) {
        ll a, b;
        cin >> a >> b;
        cout << choose(a, b) << "\n";
    }
}
```

## Generate all subset
- i&(1<<b): check whether the bth bit of i is 1 or not
- i|(1<<b): set the bth bit of i to 1
```cpp
vector<vector<int>> subsets;
for(int i = 0; i < (1<<n); i++) {
    vector<int> subset(n);
    for(int b = 0; b < n; b++) {
        if(i&(1<<b)) {
            subset[b] = 1;
        }
        // dp[i|(1<<b)]
    }
    subsets.push_back(subset);
}
```

## Generate all Permutation
- [C - Monotonically Increasing (atcoder.jp)](https://atcoder.jp/contests/abc263/tasks/abc263_c)
```cpp
/*
0 1 2
0 2 1
1 0 2
1 2 0
2 0 1
2 1 0
*/
vector<int> inp = {0,1,2}
do{
    for(int a : inp) cout << a << " ";
    cout << endl;
} while(next_permutation(inp.begin(), inp.end()));
```

## Generate all Combination
```cpp
/*
from {0,1,2} choose 2
0 1
0 2
1 2
*/
vector<int> inp(n, 1);
for(int i = 0; i < 2; i++) inp[i] = 0; // {0,0,1}
do{
    for(int i = 0; i < inp.size(); i++) {
        if(inp[i]==0) cout << i << " ";
    }
    cout << endl;
} while(next_permutation(inp.begin(), inp.end()));
```
