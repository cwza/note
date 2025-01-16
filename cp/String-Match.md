# String Match

## String Hash
### Compute Hash for 1 String
* hash(s) = ( s[0]*B^(n-1) + s[1]*B^(n-2) + ... + s[n-1]*B^0 ) mod M
* s[i]: ascii code of character s[i]
* B = prime number that roughly eaual to the number of characters in the input alphabet (ex: 133 for all ascii code)
* M: large prime number
* s=t -> hash(s)=hash(t), note that this is not if and only if, so 2 different string may have same hash with 1/M probability.


### Compute Hash for All Substring
* Precompute hash(s[0...0]), hash(s[0...1]), hash(s[0...2]), ..., hash(s[0...n-1])) just like prefix sum in O(n)
    + hash(s[0...i]) = hash(s[0...i-1])*B + s[i]
* Precompute B^0, B^1, B^2, ..., B^(n-1) in O(n)
* Hash(s[i...j]) = hash(s[0...j]) - hash(s[0...i-1])*B^(j-i+1) in O(1)


### Use Template from USACO
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

struct HashedString {
	static const long long M = 1e9+9;
	static const long long M2 = 1e9+7;
	// static const long long M3 = 972663749;
	static long long B;

	// pow[i] contains B^i % M
	static vector<long long> pow, pow2;

	// p_hash[i] is the hash of the first i characters of the given string
	vector<long long> p_hash, p_hash2;

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
long long HashedString::B = 9973;
// static mt19937 gen((uint32_t)chrono::steady_clock::now().time_since_epoch().count());
// long long HashedString::B = uniform_int_distribution<ll>(131, min(HashedString::M-1, HashedString::M2-1))(gen);

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

## KMP Algorithm: O(n)
* https://www.youtube.com/watch?v=6t_1eRO-Cqo
* prefix(s): max string that is both prefix and suffix of s and it is not s itself
    + prefix("abbab") = "ab"
    + prefix("ababa") = "aba"
    + prefix("ab") = ""
* kmp[i] = length of prefix(s[0...i])
    + ex: s: "ACBACDACBACBACDA"
    +   kmp: [0001201234534567]
* We want to find all match position that t in s
    + Compute the kmp(t+"#"+s)
    + The positions that kmp[i] == t.size() are the answers
    + id = i-2*t.size()
    + ex: t#s: "ATT#HATTIVATTI"
    +       z: [00000123001230]
* https://www.luogu.com.cn/problem/P3375
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

vector<int> get_kmp(string s) {
    int n = s.size();
    s.insert(s.begin(), ' ');
    vector<int> kmp(n+1);
    kmp[0] = -1;
    for(int i = 1; i <= n; i++) {
        int k = kmp[i-1];
        while(k>=0 && s[k+1]!=s[i]) k = kmp[k];
        kmp[i] = k+1;
    }
    kmp.erase(kmp.begin());
    return kmp;
}

void solve() {
    string s, t;
    cin >> s >> t;
    int n = s.size();
    int m = t.size();

    auto kmp = get_kmp(t+"#"+s);
    dbg(kmp);
    vector<int> ids;
    for(int i = m+1; i < n+m+1; i++) {
        if(kmp[i]==m) ids.push_back(i-2*m);
    }
    for(int i = 0; i < (int)ids.size(); i++) cout << ids[i]+1 << "\n";
    for(int i = 0; i < m; i++) cout << kmp[i] << " ";
    cout << "\n";
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

## Z Algorithm
* z[i]: max k such that s[0...k-1]==s[i...i+k-1]
    + ex: s: "ACBACDACBACBACDA"
    +     z: [-002005007002001]
* We want to find all match position that t in s
    + Compute the z(t+"#"+s)
    + The positions that z[i] == t.size() are the answers
    + id = i
    + ex: t#s: "ATT#HATTIVATTI"
    +       z: [-0000300003000]
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

vector<int> get_z(string s) {
    int n = s.size();
    vector<int> z(n);
    int x = 0, y = 0;
    for (int i = 1; i < n; i++) {
       z[i] = max(0,min(z[i-x],y-i+1));
       while (i+z[i] < n && s[z[i]] == s[i+z[i]]) {
           x = i; y = i+z[i]; z[i]++;
       }
    }
    return z;
}

void solve() {
    string s, t;
    cin >> s >> t;
    int n = s.size();
    int m = t.size();

    auto z = get_z(t+"#"+s);
    dbg(t+"#"+s);
    dbg(z);
    vector<int> ids;
    for(int i = m+1; i < n+m+1; i++) {
        if(z[i]==m) ids.push_back(i-(m+1));
    }
    for(int i = 0; i < (int)ids.size(); i++) cout << ids[i]+1 << "\n";
    cout << "\n";
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
