[[toc]]

# Binary Exponentiation

## Power (O(logb))
[https://www.youtube.com/watch?v=L-Wzglnm4dM](https://www.youtube.com/watch?v=L-Wzglnm4dM)
* Recursive
```cpp
// 3^9 = 3^4 * 3^4 * 3
long long fpow(long long a, long long b) {
    if(b==0) return 1;
    long long tmp = fpow(a, b/2);
    long long res = tmp*tmp;
    if(b%2==1) res *= a;
    return res;
}
```
* Iterative
```cpp
// 11 = (1011)
// 3^11 = 3^1 * 3^2 * 3^8
long long fpow(long long a, long long b, long long modN) {
    long long res = 1;
    while(b) {
        if(b%2) res = res*a%modN;
        a = a*a%modN;
        b /= 2;
    }
    return res;
}
```

## Modulo Inverse
```cpp
long long modinv(long long a, long long p) {
    return fpow(a, p-2, p);
}
```

## Practice
- [D - Bouquet (atcoder.jp)](https://atcoder.jp/contests/abc156/tasks/abc156_d)
