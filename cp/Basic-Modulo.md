# Basic Modulo

## Plus, Multiply, Power
- (a+b)%M = (a%M+b%M)%M
- (a·b)%M = ((a%m)·(b%M))%M
- (a^b)%M = (a%M)^(b%M)%M
```cpp
// Calculate n! % M, n is in the range of int but n! will exceed long long range
long long x = 1;
for (int i = 2; i <= n; i++) {
    x = (x*i)%M; 
}
cout << x << "\n";
```
## Minus
- (a−b)%M = (a%M−b%M+M)%M
``` cpp
long long mod_minus(long long a, long long b, long long modN) {
    // return (a%modN-b%modN+ModN)%modN;
    long long ret = a-b;
    while(ret<0) ret += modN;
    return ret;
}
```

## Divide
- M should be prime and a should be divisible by b
- (a/b)%M = (a%M * modinv(b)) % M
- Little Fermat Theorem: for any 1<=a<p (p is a prime), a^(p-1) % p = 1, more general this holds if a and p coprime
    + (a/b)%M = (a * b^(-1)) % M
    +         = (a * b^(-1)*b^(M-1)) % M,   we use Little Fermat Theorem: b^(M-1)%M = 1
    +         = (a * b^(M-2)) % M
    + b^(M-2) is our modinv
```cpp
long long fpow(long long a, long long b, long long modN) {
    long long res = 1;
    while(b) {
        if(b%2) res = res*a%modN;
        a = a*a%modN;
        b /= 2;
    }
    return res;
}

long long modinv(long long a, long long p) {
    return fpow(a, p-2, p);
}

const int M = 1e9+7; // this should be a prime number
long long a = some large number, b = some large number;
cout << "(a/b)%M = " << ( (a%M) * modinv(b, M) ) % M;
```

## Tool
``` cpp
const ll modN = 1e9+7;

long long fpow(long long a, long long b, long long modN) {
    long long res = 1;
    while(b) {
        if(b%2) res = res*a%modN;
        a = a*a%modN;
        b /= 2;
    }
    return res;
}

long long modinv(long long a, long long p) {
    return fpow(a, p-2, p);
}

struct Modulo {
    ll val;
    Modulo(): val(0) {}
    Modulo(ll _val): val(_val){
        val %= modN;
    }
    Modulo& operator+=(Modulo rhs) {
        val += rhs.val;
        val %= modN;
        return *this;
    }
    Modulo& operator-=(Modulo rhs) {
        val -= rhs.val;
        while(val<0) val += modN;
        return *this;
    }
    Modulo& operator*=(Modulo rhs) {
        val *= rhs.val;
        val %= modN;
        return *this;
    }
    Modulo& operator/=(Modulo rhs) {
        val = val*modinv(rhs.val, modN);
        val %= modN;
        return *this;
    }
    friend Modulo operator+(Modulo lhs, Modulo rhs) {
        lhs += rhs;
        return lhs;
    }
    friend Modulo operator-(Modulo lhs, Modulo rhs) {
        lhs -= rhs;
        return lhs;
    }
    friend Modulo operator*(Modulo lhs, Modulo rhs) {
        lhs *= rhs;
        return lhs;
    }
    friend Modulo operator/(Modulo lhs, Modulo rhs) {
        lhs /= rhs;
        return lhs;
    }
    friend ostream& operator<<(ostream& os, Modulo& modll) {
        os << modll.val;
        return os;
    }
};
```
