# String Hash

## Compute hash for 1 string
* hash(s) =( s[0] + s[1]*p + s[2]*p^2 + s[3]*p^3 + ... + s[n-1]*p^(n-1) ) mod m
* s[i]: ascii code but start from 1 (ex: a:1, b:2, c:3, ...)
* p = prime number that roughly eaual to the number of characters in the input alphabet (ex: 31 for lowercase, 53 for uppercase and lowercase english alphabet)
* m = large prime number (ex: 1e9+7)
* s=t -> hash(s)=hash(t), note that this is not if and only if, so 2 different string may have same hash with 1/m probability.
``` cpp
long long compute_hash(string const& s) {
    const int p = 31;
    const int m = 1e9 + 7;
    long long hash_value = 0;
    long long p_pow = 1;
    for (char c : s) {
        hash_value = (hash_value + (c - 'a' + 1) * p_pow) % m;
        p_pow = (p_pow * p) % m;
    }
    return hash_value;
}
```

## Compute hash for all substring
* Precompute hash(s[0...0]), hash(s[0...1]), hash(s[0...2]), ..., hash(s[0...n-1])) just like prefix sum in O(n)
* Precompute p^0, p^1, p^2, ..., p^(n-1) in O(n)
* hash(s[i...j]) = ( hash(s[0...j])-hash(s[0...i-1]) ) / p^i   in  O(1)
    - use modular inverse to compute this division
``` cpp
long long fpow(long long a, long long b, long long modN) {
    long long res = 1;
    while(b) {
        if(b%2) res = res*a%modN;
        a = a*a%modN;
        b /= 2;
    }
    return res;
}
long long modinv(long long a, long long pp) {
    return fpow(a, pp-2, pp);
}


int p = 31;
int m = 1e9+7;
vector<ll> pi;
void build_pi() {
    const int maxN = 2e5;
    pi.resize(maxN+1);
    ll curp = 1;
    for(int i = 0; i <= maxN; i++) {
        pi[i] = curp;
        curp = curp*p%m;
    }
}

int main() {
    build_pi();

    string s = "hksldkjfaldjf";
    vector<ll> hash(n);
    hash[0] = s[0]-'a'+1;
    for(int i = 1; i < n; i++) {
        hash[i] = hash[i-1];
        hash[i] += (s[i]-'a'+1)*pi[i]%m;
        hash[i] %= m;
    }

    int j = 7, i = 2;
    ll hashij = hash[j]-hash[i-1];
    while(hashij<0) hashij += m;
    hashij %= m;
    hashij = hashij*modinv(pi[i], m)%m;
}
```

## Use Template from USACO
* https://cses.fi/problemset/task/1753
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

class HashedString {
  private:
	// change M and B if you want
	static const long long M = 1e9+9;
	static const long long M2 = 1e9+7;
	static const long long B = 9973;

	// pow[i] contains B^i % M
	static vector<long long> pow, pow2;

	// p_hash[i] is the hash of the first i characters of the given string
	vector<long long> p_hash, p_hash2;

  public:
    // O(N)
	HashedString(const string &s) : p_hash(s.size() + 1), p_hash2(s.size() + 1) {
		while (pow.size() <= s.size()) { pow.push_back((pow.back() * B) % M); }
		p_hash[0] = 0;
		for (int i = 0; i < (int)s.size(); i++) {
			p_hash[i + 1] = ((p_hash[i] * B) % M + s[i]) % M;
		}

		while (pow2.size() <= s.size()) { pow2.push_back((pow2.back() * B) % M2); }
		p_hash2[0] = 0;
		for (int i = 0; i < (int)s.size(); i++) {
			p_hash2[i + 1] = ((p_hash2[i] * B) % M2 + s[i]) % M2;
		}
	}

    // O(1)
	long long get_hash(int start, int end) {
		long long raw_val = (p_hash[end + 1] - (p_hash[start] * pow[end - start + 1]));
		return (raw_val % M + M) % M;
	}
	long long get_hash2(int start, int end) {
		long long raw_val = (p_hash2[end + 1] - (p_hash2[start] * pow2[end - start + 1]));
		return (raw_val % M2 + M2) % M2;
	}
};
vector<long long> HashedString::pow = {1};
vector<long long> HashedString::pow2 = {1};
// Uncomment following two lines which will randomly choose B to prevent open hash hack if you want to use it on Codeforces.
// mt19937 rng((uint32_t)chrono::steady_clock::now().time_since_epoch().count());
// const ll HashedString::B = uniform_int_distribution<ll>(0, M - 1)(rng);

void solve() {
    string s, t;
    cin >> s >> t;
    int n = s.size();
    int m = t.size();
    HashedString hs_s(s);
    HashedString hs_t(t);
    ll hash_t = hs_t.get_hash(0, m-1);
    ll hash_t2 = hs_t.get_hash2(0, m-1);
    int ans = 0;
    for(int i = 0; i+m-1 < n; i++) {
        if(hs_s.get_hash(i, i+m-1)==hash_t && hs_s.get_hash2(i, i+m-1)==hash_t2) ans++;
    }
    cout << ans << "\n";
}
 
bool multi = 0;
int main() {
    ios_base::sync_with_stdio(0);
    cin.tie(0);
 
    int t = 1;
    if (multi)
        cin >> t;
    for (int i = 0; i < t; i++) {
        solve();
    }
}
```
