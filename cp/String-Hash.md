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
