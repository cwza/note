[[toc]]

# Number Theory

## Useful Property
- lcm(a, b) = a*b/gcd(a, b)
- gcd(a, 0) = a, if a > 0
- gcd(a, b) = gcd(a-b, b) if b <= a
    + gcd(a, b) = gcd(a%b, b) if b <= a
- if a, b coprime then a-b and b is also coprime
    + if a, b coprime then a%b and b is also coprime
- a % b < a/2, if a >= b
- if a%m==b%m then (a-b)%m=0
- supose gcd(a, b) = g, we can always find two integers x, y(include negative and zero) such that `x*a + y*b = z*g, z is any non-negative integer`, that is all multiplies of g can be composed from a and b.
    + this is called Bezout Lemma
- number of prime  less than n ~ n/ln(n)
- number of factors for n ~ cube_root(n)
- number of distinct prime factors n ~ lnln(n)

## Find gcd(a, b) by Euclid’s algorithm
- gcd(a, b) = a if b = 0
- gcd(a, b) = gcd(b, a%b) if b ≠ 0
- Time Complexity: O( log(min(a, b)) )
    + a % b < a/2 if a >= b
- lcm(a, b) = (a*b)/gcd(a,b);
```cpp
int gcd(int a, int b) {
       if (b == 0) return a;
       return gcd(b, a%b);
}
ll lcm(int a, int b) {
    return (ll)a/gcd(a, b)*b;
}
```

## Find all factors of M, O(sqrtM)
```cpp
vector<int> factor;
for(int i = 1; i*i <= M; i++) {
    if(M%i==0) {
        factor.push_back(i);
				if(i*i!=M) factor.push_back(M/i);
    }
}
sort(factor.begin(), factor.end());
```

## Find all factors of 1 … N in Theta(NlogN)
* Idea: add 1 to all 1's multiplier, add 2 to all 2's multiplier, add 3 to all 3's multiplier, .....etc
* Why Theta(NlogN)???
    + Our computation will be n/1 + n/2 + n/3 + .. + n/n = n(1 + 1/2 + 1/3 + ... + 1/n)
        - 1 + 1/2 + 1/3 + ... + 1/n is called Harmonic Series(Denote as Hn), this value is around Theta(logn)
            + We can bound Harmonic Series by this inequality: logn/2 <= Hn <= logn
        - so n*Hn should be Theta(nlogn)
    + Show Hn <= logn = O(logn)
        - 1 + 1/2 + ... + 1/n = 1 + (1/2 + 1/3) + (1/4 + 1/5 + 1/6 + 1/7) + ...... <= 1 + (1/2 + 1/2) + (1/4 + 1/4 + 1/4 + 1/4) + ..... = 1 + 1 + 1 + .... = log(n+1)
            + This number is just the height of a complete binary tree which have n nodes
        - So Hn <= log(n+1) = O(logn)
    + Show Hn >= logn/2 = Omega(logn)
        - 1 + 1/2 + ... + 1/n = 1 + 1/2 + (1/3 + 1/4) + (1/5 + 1/6 + 1/7 + 1/8) + ...... >= 1/2 + 1/2 + (1/4 + 1/4) + (1/8 + 1/8 + 1/8 + 1/8) + .... = 1/2 + 1/2 + 1/2 + .... = log(n+1)/2
        - So Hn >= log(n+1)/2 = Omega(logn)
    + Another proof by integration shows Hn <= O(logn)
        - 1 + 1/2 + ... + 1/n = 1 + (1/2 + ... + 1/n) <= 1 + integrate 1/x from 1 to n = 1+logn
        - So Hn <= 1+logn = O(logn)
```cpp
// factors[1] = {1}, factors[2] = {1,2}, factors[6] = {1,2,3,6}
const int maxN = 1e5;
vector<int> factors[maxN+1];

for(int i = 1; i <= maxN; i++) {
    for(int j = i; j <= maxN; j += i) {
        factors[j].push_back(i);
    }
}
```

## Prime factorization O(square_root(n))
```cpp
vector<array<int, 2>> ans;
for(int i = 2; i*i <= N; i++ {
    int cnt = 0;
    while(N%i==0) {
        N /= i;
        cnt++;
    }
    if(cnt) ans.push_back(i, cnt);
}
if(N>1) ans.push_back(N, 1);
```

## Find primes by sieve O(nlogn)
```cpp
const int n = 20;
int prime[n+1];
fill(prime, prime+n+1, 1);
prime[0] = 0;
prime[1] = 0;
for (int i = 2; i <= n; i++) {
    // if (!prime[i]) continue;
    for (int j = 2*i; j <= n; j += i) {
        prime[j] = 0;
    }
}
```

## Find all prime factors of X in O(logN) by modified sieve
```cpp
// precompute spf[i] in O(nlogn): smallest prime factor for i
const int maxX = 1e7;
int spf[maxX+1];
for(int i = 2; i <= maxX; i++) {
    // if(spf[i]) continue;
    for(int j = i; j <= maxX; j+=i) {
        if(spf[j]) continue;
        spf[j] = i;
    }
}

// get all prime factors of X in logX
int x = 1500;
// set<int> primes;
vector<int> primes;
while(x>1) {
    // primes.insert(spf[x]);
    if(!ans.size() || ans.back()!=spf[x]) primes.push_back(spf[x]);
    x /= spf[x];
}

// prime factorization of X in logX
int x = 1500;
// map<int, int> ans;
vector<pair<int, int>> ans
while(x>1) {
    // ans[spf[x]]++;
    if(ans.size() && ans.back().first==spf[x]) ans.back().second++;
    else ans.push_back({spf[x], 1});
    x /= spf[x];
}
```

## Given a, and we know b is in [1, n], find number of b such that gcd(a, b) = 1
* Use prime factorization to find the prime factors of a, denote as p1, p2, ...., pk
* By Inclusion and Exclusion, the answer will be ` n - n/p1 - n/p2 - .... - n/pk + n/(p1*p2) + n/(p1*p3) + ... - n/(p1*p2*p3) - n/(...) + ... `
    - Note that these divisions are actually divide and take floor
    - we add all elements which is n (k choose 0)
    - we minus the elements which are divisible by `p1, p2, p3, ...` (k choose 1)
    - we add back the elements which are divisible by `p1*p2, p1*p3, ...` (k choose 2)
    - ... (k choose k)
* Implementation
    - Generate all subset of p1, p2, ..., pk
    - minus if the subset size is odd
    - add if the subset size is even
``` cpp
vector<int> get_primes(int a) {
    ...
}
int main() {
    int n, a;
    cin >> n >> a;
    vector<int> primes = get_primes(a);
    int m = primes.size();
    ll ans = 0;
    for(int i = 0; i < (1<<m); i++) {
        ll cur = 1;
        for(int j = 0; j < m; j++) {
            if((1<<j)&i) {
                cur *= primes[j];
            }
        }
        if(__builtin_popcount(i)%2==1) ans -= n/cur;
        else ans += n/cur;
    }
    cout << ans << "\n";
}
```

## Find the smallest x such that x in [a, b] and x divides by p, also find the largest in O(1)
- Smallest: ceil(a/p) * p
- Largest: floor(b/p) * p
```cpp
int smallest_ans = (a+p-1)/p * p;
int largest_ans = b/p * p
```
