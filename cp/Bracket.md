# Bracket Sequence

## Conditions for a Valid Bracket Sequence
* The total number of open bracket = The total number of close bracket
* The prefix of opening bracket >= The prefix of closing bracket for all positions
    + Prefix balance = prefix of opening bracket - prefix of closing bracket 
    + Prefix balance >= 0 for all positions
```
Ex: 
Sequence:        (()())
Open prefix:    0122333
Close prefix:   0001123
Balance prefix: 0121210
```

## Important Property of a Non-Valid Bracket Sequence
* The position that has the minimum balance is an important position, call it P
* All the opening brackets in the left half of P will be matched with closing brackets in the left half
* All the closing brackets in the right half of P will be matched with opening brackets in the right half
```
Ex1:
Index:    0  1  2  3  4  5  6| 7  8  9 10 11 12
Balance:  0  1  2  1  0 -1 -2|-1  0 -1  0  1  0
Sequence:    (  (  )  )  )  )| (  (  )  (  (  )
             -  -  -  -      | -  -  -        -
All the opening brackets before index 6 have been matched by the closing brackets before index 6
All the closing brackets after index 6 have been matched by the opening brackest after index 6

Ex2:
Index:    0|1 2 3 4 5
Balance:  0|1 2 3 2 1
Sequence:  |( ( ( ) )
           |  - - - -
All the opening brackets before index 0 have been matched by the closing brackets before index 0
All the closing brackets after index 0 have been matched by the opening brackest after index 0
```
