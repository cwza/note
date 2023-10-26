# Basic Modulo

## Plus Minus multiply power
- (a+b)%M = (a%M+b%M)%M
- (a−b)%M = (a%M−b%M+M)%M
- (a·b)%M = ((a%m)·(b%M))%M
- (a^b)%M = (a%M)^(b % M)%M
```cpp
// Calculate n! % M, n is in the range of int but n! will exceed long long range
long long x = 1;
for (int i = 2; i <= n; i++) {
    x = (x*i)%M; 
}
cout << x << "\n";
```

## Divide
- (a/b)%M = (a%M * modinv(b)) % M
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
