[[toc]]
# Segment Tree by AtCoder Library
* https://github.com/atcoder/ac-library
* https://atcoder.github.io/ac-library/production/document_en/index.html

## Point Update by Segment Tree
* [CSES Dynamic Range Sum Queries](https://cses.fi/problemset/task/1648)
``` cpp
#include <bits/stdc++.h>
using namespace std;
#ifdef DEBUG
    #include "debug.hpp"
#else
    #define dbg(x)
#endif
using ll = long long;

namespace atcoder {
namespace internal {

#if __cplusplus >= 202002L

using std::bit_ceil;

#else

// @return same with std::bit::bit_ceil
unsigned int bit_ceil(unsigned int n) {
    unsigned int x = 1;
    while (x < (unsigned int)(n)) x *= 2;
    return x;
}

#endif

// @param n `1 <= n`
// @return same with std::bit::countr_zero
int countr_zero(unsigned int n) {
#ifdef _MSC_VER
    unsigned long index;
    _BitScanForward(&index, n);
    return index;
#else
    return __builtin_ctz(n);
#endif
}

// @param n `1 <= n`
// @return same with std::bit::countr_zero
constexpr int countr_zero_constexpr(unsigned int n) {
    int x = 0;
    while (!(n & (1 << x))) x++;
    return x;
}

}  // namespace internal

#if __cplusplus >= 201703L

template <class S, auto op, auto e> struct segtree {
    static_assert(std::is_convertible_v<decltype(op), std::function<S(S, S)>>,
                  "op must work as S(S, S)");
    static_assert(std::is_convertible_v<decltype(e), std::function<S()>>,
                  "e must work as S()");

#else

template <class S, S (*op)(S, S), S (*e)()> struct segtree {

#endif

  public:
    segtree() : segtree(0) {}
    explicit segtree(int n) : segtree(std::vector<S>(n, e())) {}
    explicit segtree(const std::vector<S>& v) : _n(int(v.size())) {
        size = (int)internal::bit_ceil((unsigned int)(_n));
        log = internal::countr_zero((unsigned int)size);
        d = std::vector<S>(2 * size, e());
        for (int i = 0; i < _n; i++) d[size + i] = v[i];
        for (int i = size - 1; i >= 1; i--) {
            update(i);
        }
    }

    void set(int p, S x) {
        assert(0 <= p && p < _n);
        p += size;
        d[p] = x;
        for (int i = 1; i <= log; i++) update(p >> i);
    }

    S get(int p) const {
        assert(0 <= p && p < _n);
        return d[p + size];
    }

    S prod(int l, int r) const {
        assert(0 <= l && l <= r && r <= _n);
        S sml = e(), smr = e();
        l += size;
        r += size;

        while (l < r) {
            if (l & 1) sml = op(sml, d[l++]);
            if (r & 1) smr = op(d[--r], smr);
            l >>= 1;
            r >>= 1;
        }
        return op(sml, smr);
    }

    S all_prod() const { return d[1]; }

    template <bool (*f)(S)> int max_right(int l) const {
        return max_right(l, [](S x) { return f(x); });
    }
    template <class F> int max_right(int l, F f) const {
        assert(0 <= l && l <= _n);
        assert(f(e()));
        if (l == _n) return _n;
        l += size;
        S sm = e();
        do {
            while (l % 2 == 0) l >>= 1;
            if (!f(op(sm, d[l]))) {
                while (l < size) {
                    l = (2 * l);
                    if (f(op(sm, d[l]))) {
                        sm = op(sm, d[l]);
                        l++;
                    }
                }
                return l - size;
            }
            sm = op(sm, d[l]);
            l++;
        } while ((l & -l) != l);
        return _n;
    }

    template <bool (*f)(S)> int min_left(int r) const {
        return min_left(r, [](S x) { return f(x); });
    }
    template <class F> int min_left(int r, F f) const {
        assert(0 <= r && r <= _n);
        assert(f(e()));
        if (r == 0) return 0;
        r += size;
        S sm = e();
        do {
            r--;
            while (r > 1 && (r % 2)) r >>= 1;
            if (!f(op(d[r], sm))) {
                while (r < size) {
                    r = (2 * r + 1);
                    if (f(op(d[r], sm))) {
                        sm = op(d[r], sm);
                        r--;
                    }
                }
                return r + 1 - size;
            }
            sm = op(d[r], sm);
        } while ((r & -r) != r);
        return 0;
    }

  private:
    int _n, size, log;
    std::vector<S> d;

    void update(int k) { d[k] = op(d[2 * k], d[2 * k + 1]); }
};
} // namespace atcoder

using S = ll; // what your segment tree node store
// how to merge 2 subtree(segments)?? 
// Ans: + for sum query and min for minimum query
S op(S lhs, S rhs) { 
    return lhs + rhs;
}
// when a subtree is empty, what it should return when be merged??
// Ans: 0 for sum query and 2e9 for minimum query
S e() {
    return 0;
}

struct SegmentTree {
    int n;
    atcoder::segtree<S, op, e> st;
    SegmentTree(vector<S> &a): n(a.size()), st(a) {
    }
    void update(int l, S val) {
        if(l>=n) return;
        st.set(l, val);
    }
    S query(int l, int r) {
        if(l>r) return 0;
        return st.prod(l, r+1);
    }
};
struct SegmentTreeBF {
    int n;
    vector<ll> a;
    SegmentTreeBF(vector<ll> &_a): n(_a.size()), a(_a) {
    }
    void update(int l, ll val) {
        if(l>=n) return;
        a[l] = val;
    }
    ll query(int l, int r) {
        if(l>r) return 0;
        ll ans = 0;
        for(int i = l; i <= r; i++) ans += a[i];
        return ans;
    }
};

void solve() {
    int n, q;
    cin >> n >> q;
    vector<ll> inp(n);
    for(int i = 0; i < n; i++) cin >> inp[i];
    
    SegmentTree st(inp);
    while(q--) {
        int mode, a, b;
        cin >> mode >> a >> b;
        if(mode==1) {
            a--;
            st.update(a, b);
        } else {
            a--; b--;
            cout << st.query(a, b) << "\n";
        }
    }
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

## Range Update by Lazy Segment Tree
* [CSES Range Update Queries](https://cses.fi/problemset/task/1651)
``` cpp
#include <bits/stdc++.h>
using namespace std;
#ifdef DEBUG
    #include "debug.hpp"
#else
    #define dbg(x)
#endif
using ll = long long;

namespace atcoder {
namespace internal {

#if __cplusplus >= 202002L

using std::bit_ceil;

#else

// @return same with std::bit::bit_ceil
unsigned int bit_ceil(unsigned int n) {
    unsigned int x = 1;
    while (x < (unsigned int)(n)) x *= 2;
    return x;
}

#endif

// @param n `1 <= n`
// @return same with std::bit::countr_zero
int countr_zero(unsigned int n) {
#ifdef _MSC_VER
    unsigned long index;
    _BitScanForward(&index, n);
    return index;
#else
    return __builtin_ctz(n);
#endif
}

// @param n `1 <= n`
// @return same with std::bit::countr_zero
constexpr int countr_zero_constexpr(unsigned int n) {
    int x = 0;
    while (!(n & (1 << x))) x++;
    return x;
}

}  // namespace internal

#if __cplusplus >= 201703L

template <class S,
          auto op,
          auto e,
          class F,
          auto mapping,
          auto composition,
          auto id>
struct lazy_segtree {
    static_assert(std::is_convertible_v<decltype(op), std::function<S(S, S)>>,
                  "op must work as S(S, S)");
    static_assert(std::is_convertible_v<decltype(e), std::function<S()>>,
                  "e must work as S()");
    static_assert(
        std::is_convertible_v<decltype(mapping), std::function<S(F, S)>>,
        "mapping must work as S(F, S)");
    static_assert(
        std::is_convertible_v<decltype(composition), std::function<F(F, F)>>,
        "composition must work as F(F, F)");
    static_assert(std::is_convertible_v<decltype(id), std::function<F()>>,
                  "id must work as F()");

#else

template <class S,
          S (*op)(S, S),
          S (*e)(),
          class F,
          S (*mapping)(F, S),
          F (*composition)(F, F),
          F (*id)()>
struct lazy_segtree {

#endif

  public:
    lazy_segtree() : lazy_segtree(0) {}
    explicit lazy_segtree(int n) : lazy_segtree(std::vector<S>(n, e())) {}
    explicit lazy_segtree(const std::vector<S>& v) : _n(int(v.size())) {
        size = (int)internal::bit_ceil((unsigned int)(_n));
        log = internal::countr_zero((unsigned int)size);
        d = std::vector<S>(2 * size, e());
        lz = std::vector<F>(size, id());
        for (int i = 0; i < _n; i++) d[size + i] = v[i];
        for (int i = size - 1; i >= 1; i--) {
            update(i);
        }
    }

    void set(int p, S x) {
        assert(0 <= p && p < _n);
        p += size;
        for (int i = log; i >= 1; i--) push(p >> i);
        d[p] = x;
        for (int i = 1; i <= log; i++) update(p >> i);
    }

    S get(int p) {
        assert(0 <= p && p < _n);
        p += size;
        for (int i = log; i >= 1; i--) push(p >> i);
        return d[p];
    }

    S prod(int l, int r) {
        assert(0 <= l && l <= r && r <= _n);
        if (l == r) return e();

        l += size;
        r += size;

        for (int i = log; i >= 1; i--) {
            if (((l >> i) << i) != l) push(l >> i);
            if (((r >> i) << i) != r) push((r - 1) >> i);
        }

        S sml = e(), smr = e();
        while (l < r) {
            if (l & 1) sml = op(sml, d[l++]);
            if (r & 1) smr = op(d[--r], smr);
            l >>= 1;
            r >>= 1;
        }

        return op(sml, smr);
    }

    S all_prod() { return d[1]; }

    void apply(int p, F f) {
        assert(0 <= p && p < _n);
        p += size;
        for (int i = log; i >= 1; i--) push(p >> i);
        d[p] = mapping(f, d[p]);
        for (int i = 1; i <= log; i++) update(p >> i);
    }
    void apply(int l, int r, F f) {
        assert(0 <= l && l <= r && r <= _n);
        if (l == r) return;

        l += size;
        r += size;

        for (int i = log; i >= 1; i--) {
            if (((l >> i) << i) != l) push(l >> i);
            if (((r >> i) << i) != r) push((r - 1) >> i);
        }

        {
            int l2 = l, r2 = r;
            while (l < r) {
                if (l & 1) all_apply(l++, f);
                if (r & 1) all_apply(--r, f);
                l >>= 1;
                r >>= 1;
            }
            l = l2;
            r = r2;
        }

        for (int i = 1; i <= log; i++) {
            if (((l >> i) << i) != l) update(l >> i);
            if (((r >> i) << i) != r) update((r - 1) >> i);
        }
    }

    template <bool (*g)(S)> int max_right(int l) {
        return max_right(l, [](S x) { return g(x); });
    }
    template <class G> int max_right(int l, G g) {
        assert(0 <= l && l <= _n);
        assert(g(e()));
        if (l == _n) return _n;
        l += size;
        for (int i = log; i >= 1; i--) push(l >> i);
        S sm = e();
        do {
            while (l % 2 == 0) l >>= 1;
            if (!g(op(sm, d[l]))) {
                while (l < size) {
                    push(l);
                    l = (2 * l);
                    if (g(op(sm, d[l]))) {
                        sm = op(sm, d[l]);
                        l++;
                    }
                }
                return l - size;
            }
            sm = op(sm, d[l]);
            l++;
        } while ((l & -l) != l);
        return _n;
    }

    template <bool (*g)(S)> int min_left(int r) {
        return min_left(r, [](S x) { return g(x); });
    }
    template <class G> int min_left(int r, G g) {
        assert(0 <= r && r <= _n);
        assert(g(e()));
        if (r == 0) return 0;
        r += size;
        for (int i = log; i >= 1; i--) push((r - 1) >> i);
        S sm = e();
        do {
            r--;
            while (r > 1 && (r % 2)) r >>= 1;
            if (!g(op(d[r], sm))) {
                while (r < size) {
                    push(r);
                    r = (2 * r + 1);
                    if (g(op(d[r], sm))) {
                        sm = op(d[r], sm);
                        r--;
                    }
                }
                return r + 1 - size;
            }
            sm = op(d[r], sm);
        } while ((r & -r) != r);
        return 0;
    }

  private:
    int _n, size, log;
    std::vector<S> d;
    std::vector<F> lz;

    void update(int k) { d[k] = op(d[2 * k], d[2 * k + 1]); }
    void all_apply(int k, F f) {
        d[k] = mapping(f, d[k]);
        if (k < size) lz[k] = composition(f, lz[k]);
    }
    void push(int k) {
        all_apply(2 * k, lz[k]);
        all_apply(2 * k + 1, lz[k]);
        lz[k] = id();
    }
};

}  // namespace atcoder

using S = ll; // what your segment tree node store
// how to merge 2 subtree(segments)?? 
// Ans: + for sum query and min for minimum query
S op(S lhs, S rhs) {
    return lhs + rhs;
}
// when a subtree is empty, what it should return when be merged??
// Ans: 0 for sum query and 2e9 for minimum query
S e() {
    return 0;
}
using F = ll; // data that you want to apply to your segment
// what should be do when you apply l then r
F composition(F l, F r) {
    return l + r;
}
// if you apply id to segment, nothing would change
F id() {
    return 0;
}
// How does S changed when you apply F to it
S mapping(F f, S x) {
    return x + f;
}

struct SegmentTree {
    int n;
    atcoder::lazy_segtree<S, op, e, F, mapping, composition, id> st;
    SegmentTree(vector<S> &a): n(a.size()), st(a) {
    }
    void update(int l, int r, F val) {
        if(l>=n) return;
        st.apply(l, r+1, val);
    }
    S query(int l, int r) {
        if(l>r) return 0;
        return st.prod(l, r+1);
    }
};
struct SegmentTreeBF {
    int n;
    vector<ll> a;
    SegmentTreeBF(vector<ll> &_a): n(_a.size()), a(_a) {
    }
    void update(int l, int r, ll val) {
        if(l>r) return;
        for(int i = 0; i <= r; i++) a[i] += val;
    }
    ll query(int l, int r) {
        if(l>r) return 0;
        ll ans = 0;
        for(int i = l; i <= r; i++) ans += a[i];
        return ans;
    }
};

void solve() {
    int n, q;
    cin >> n >> q;
    vector<ll> inp(n);
    for(int i = 0; i < n; i++) cin >> inp[i];
    
    SegmentTree segtree(inp);
    while(q--) {
        int mode;
        cin >> mode;
        if(mode==1) {
            int a, b, u;
            cin >> a >> b >> u;
            a--; b--;
            segtree.update(a, b, u);
        } else {
            int k;
            cin >> k;
            k--;
            cout << segtree.query(k, k) << "\n";
        }
    }

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

## Others
* From l to n find the first j such that a[l]+a[l+1]+a[l+2]+...+a[j] >= k, if no such j then return n
* From r to 0 find the first j such that a[r]+a[r-1]+a[r-2]+...+a[j] >= k, if no such j then return -1
``` cpp
using S = ll; // what your segment tree node store
// how to merge 2 subtree(segments)?? 
// Ans: + for sum query and min for minimum query
S op(S lhs, S rhs) {
    return lhs + rhs;
}
// when a subtree is empty, what it should return when be merged??
// Ans: 0 for sum query and 2e9 for minimum query
S e() {
    return 0;
}
using F = ll; // data that you want to apply to your segment
// what should be do when you apply l then r
F composition(F l, F r) {
    return l + r;
}
// if you apply id to segment, nothing would change
F id() {
    return 0;
}
// How does S changed when you apply F to it
S mapping(F f, S x) {
    return x + f;
}

struct SegmentTree {
    int n;
    atcoder::lazy_segtree<S, op, e, F, mapping, composition, id> st;
    SegmentTree(vector<ll> a): n(a.size()), st(a) {
    }
    void add(int l, F val) {
        st.apply(l, l+1, val);
    }
    int next(int l, ll k) {
        auto g = [&](ll x) -> bool {
            return x < k;
        };
        // max_right will return the first j such that g(op(a[l], a[l+1], a[l+2], ..., a[j])) is false
        return st.max_right<decltype(g)>(l, g);
    }
    int prev(int r, ll k) {
        auto g = [&](ll x) -> bool {
            return x < k;
        };
        // min_left will return the last j such that g(op(a[r-1], a[r-2], a[r-3], ..., a[j])) is true
        return st.min_left<decltype(g)>(r+1, g)-1;
    }
};

int main() {
    vector<int> a = {2,3,2,1,3,5,6,4,3,7};
    SegmentTree st(a); 
    cout << st.next(4, 16); // 7
    cout << st.prev(4, 8); // 2 
}

```
