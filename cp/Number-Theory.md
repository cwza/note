[[toc]]

# Number Theory

## Useful Property
- lcm(a, b) = ab/gcd(a, b)
- gcd(a, b) = gcd(a, b-a) if a ≤ b
- number of prime  less than n ~ n/ln(n)
- number of factors for n ~ cube_root(n)
- number of distinct prime factors n ~ lnln(n)

## Find gcd(a, b) by Euclid’s algorithm
- gcd(a,b) = a if b = 0
- gcd(a,b) = gcd(b, a%b) if b ≠ 0
```cpp
int gcd(int a, int b) {
       if (b == 0) return a;
       return gcd(b, a%b);
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

## Find all factors of 1 … N in O(NloglogN)
add 2 to all 2's multiplier, add 3 to all 3's multiplier, .....etc
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

## Prime factorization
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

## Find primes by sieve (nloglogn)
```cpp
const int n = 20;
int sieve[n+1];
for (int x = 2; x <= n; x++) {
    if (sieve[x]) continue;
    for (int u = 2*x; u <= n; u += x) {
        sieve[u] = x;
    }
}
// sieve[]: 
// 2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20
// 0  0  2  0  3  0  2  3  5  0  3  0  7  5  2  0  3  0  5
```

## Find all primes in logN by modified sieve
```cpp
// precompute lpf[i]: smallest prime factor for i
const int maxX = 1e7;
int spf[maxX+1];
for(int i = 2; i <= maxX; i++) {
    if(spf[i]) continue;
    for(int j = i; j <= maxX; j+=i) {
        if(spf[j]) continue;
        spf[j] = i;
    }
}

// get all primes of x in logN
int x = 1500;
set<int> primes;
while(x>1) {
    primes.insert(spf[x]);
    x /= spf[x];
}
```

## Find the smallest x such that x in [a, b] and x divides by p, also find the largest in O(1)
- Smallest: ceil(a/p) * p
- Largest: floor(b/p) * p
```cpp
int smallest_ans = (a+p-1)/p * p;
int largest_ans = b/p * p
```
