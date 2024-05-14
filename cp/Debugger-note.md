
## Example Code
``` cpp
#include <bits/stdc++.h>
using namespace std;

int ga[3] = {3, 2, 1};
vector<int> gva = {1, 2, 3};
int main() {
    vector<int> va = {1, 2, 3, 4};
    int aa[4] = {4, 3, 2, 1};
    map<int, int> ma = {{1, 1}, {2, 2}};
    set<int> sa = {1, 2, 3};

    cout << aa[0] << va[0] << ma[1] << *sa.begin();
}
```

## Gdb
``` sh
g++ main.cpp -o main.out -std=c++17 -g
gdb main.out # Debug a.out
br main.cpp:12 # Set break point at main.cpp line 12
run # Start the program
f # See the current exec line
p aa # Print the aa array
p va[0] # Print the first element of va
p ma[0]
p *sa.begin()
br main.cpp:13 # Add another break point
next # Go to the next line
nexti # Go to the next asm line
step # Step into function
q # Exit
```

## LLDB
``` sh
clang++ main.cpp -o main.out -std=c++17 -g
lldb main.out # Debug a.out
b main.cpp:12 # Set break point at main.cpp line 12
run # Start the program
f # See the current exec line
p aa # Print the aa array
p va[0] # Print the first element of va
p ma[0]
p *sa.begin()
br main.cpp:13 # Add another break point
br l # List breakpoints
br del 2 # Delete the 2nd breakpoint
next # Go to the next line
step # Step into function
q # Exit
gui # Enter GUI mode
```
