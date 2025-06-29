# Bit Operations

## Neat fact
* A xor B = (A or B) - (A and B) = (A + B) - 2*(A and B)
* 2k xor 2k+1 = 1, (given 2 consecutive numbers which is even and odd, their xor will be 1, because the only difference of their binary expression is at the last position)
* -A = ~A+1
* We can use `A & -A` to extract the right most 1 from A (Brian Kernighan Algorithm)
    + Ex: A  = 01101000
    +    -A  = 10011000 (~A+1)
    + A&(-A) = 00001000 (extract the right most 1 from A)
* 0 xor 1 xor 2 xor 3 = 0, 4 xor 5 xor 6 xor 7 = 0, ...., so the consecutive xor cycle by 4
* Given an array A, the `A[l] xor ... xor A[r] = (A[0] xor ... xor A[r]) xor (A[0] xor ... xor A[l-1]) = pref_xor(r) xor pref_xor(l-1)`
### Given l and r, compute l xor l+1 xor ... xor r-1 xor r in O(1)
``` cpp
ll f(ll n) { // prefix xor from 0 to n
    if(n%4==0) return n;
    if(n%4==1) return 1;
    if(n%4==2) return n^1;
    if(n%4==3) return 0;
    assert(0);
}
ll g(ll l, ll r) { // xor from l to r
    return f(r) ^ f(l-1);
}
int main() {
    ll l, r;
    cin >> l >> r;
    cout << g(l, r) << "\n"; // l xor l+1 xor ... xor r-2 xor r
}
```

## Basic functions
- __builtin_clz(*x*): the number of zeros at the beginning of the number
- __builtin_ctz(*x*): the number of zeros at the end of the number
- __builtin_popcount(*x*): the number of ones in the number
- __builtin_parity(*x*): the parity (even or odd) of the number of ones
```cpp
int x = 5328; // 00000000000000000001010011010000
cout << __builtin_clz(x) << "\n"; // 19
cout << __builtin_ctz(x) << "\n"; // 4
cout << __builtin_popcount(x) << "\n"; // 5
cout << __builtin_parity(x) << "\n"; // 1
```

## Use these function to find lg(x)
- floor(lg(x)) = 31 - __builtin_clz(x), if x ≥ 0
- ceil(lg(x)) = 32 - __builtin_clz(x), if x ≥ 0

## BitSet
* https://www.bilibili.com/video/BV1yr4y1Z7jb/
``` cpp
struct Bitset {
    int sz;
    vector<int> st;
    Bitset(int _sz): sz(_sz), st((_sz+31)/32) {}
    bool test(int idx) {
        if(st[idx/32] & (1<<(idx%32))) return 1;
        return 0;
    }
    void set(int idx) {
        st[idx/32] |= 1<<(idx%32);
    }
    void unset(int idx) {
        st[idx/32] &= ~(1<<(idx%32));
    }
    void flip(int idx) {
        st[idx/32] ^= 1<<(idx%32);
    }
    string to_string() {
        string str;
        for(int i = 0; i < sz; i++) {
            if(test(i)) str += '1';
            else str += '0';
        }
        return str;
    }
};
```
