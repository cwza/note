# Master Theorem

- T(n) = a*T(n/b) + f(n), f(n) should be polynomial
    - case1: n^logba = f(n) then T(n) = O(n^logba*lgn)
    - case2: n^logba < f(n) then T(n) = O(f(n))
    - case3: n^logba > f(n) then T(n) = O(n^logba)
- T(n) = a*T(n/b) + nlogn => O(n(logn)^2)
    - Although this case can be solved by extended master theorem, I suggest to just memorize this special case.
