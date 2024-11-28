# Greedy

## Question 1
We have an integer array B = [B1, B2, ..., Bn] with us. Each Bi is >= 0.
In one move, we can subtract 1 from at most M different indices of B, and we’d like to find the minimum number of moves to reduce all elements to 0.
* The solution is max( mx, ceil(sum/M) ), where mx = max(Bi), sum = sum(Bi)
