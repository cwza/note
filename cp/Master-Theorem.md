# Master Theorem

- T(n) = aT(n/b) + f(n)
    - case1: n^logba = f(n) then T(n) = O(n^logba*lgn)
    - case2: n^logba < f(n) then T(n) = O(f(n))
    - case3: n^logba > f(n) then T(n) = O(n^logba)