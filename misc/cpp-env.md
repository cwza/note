[[toc]]

# Mac M1 C++

## Use bits/stdc++ in clang
- add the following file to /Users/cwza/cwza/cp/include/bits/stdc++.h
```cpp
// C++ includes used for precompiling -*- C++ -*-

// Copyright (C) 2003-2014 Free Software Foundation, Inc.
//
// This file is part of the GNU ISO C++ Library.  This library is free
// software; you can redistribute it and/or modify it under the
// terms of the GNU General Public License as published by the
// Free Software Foundation; either version 3, or (at your option)
// any later version.

// This library is distributed in the hope that it will be useful,
// but WITHOUT ANY WARRANTY; without even the implied warranty of
// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
// GNU General Public License for more details.

// Under Section 7 of GPL version 3, you are granted additional
// permissions described in the GCC Runtime Library Exception, version
// 3.1, as published by the Free Software Foundation.

// You should have received a copy of the GNU General Public License and
// a copy of the GCC Runtime Library Exception along with this program;
// see the files COPYING3 and COPYING.RUNTIME respectively.  If not, see
// <http://www.gnu.org/licenses/>.

/** @file stdc++.h
 *  This is an implementation file for a precompiled header.
 */

// 17.4.1.2 Headers

// C
#ifndef _GLIBCXX_NO_ASSERT
#include <cassert>
#endif
#include <cctype>
#include <cerrno>
#include <cfloat>
#include <ciso646>
#include <climits>
#include <clocale>
#include <cmath>
#include <csetjmp>
#include <csignal>
#include <cstdarg>
#include <cstddef>
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <ctime>

#if __cplusplus >= 201103L
#include <ccomplex>
#include <cfenv>
#include <cinttypes>
#include <cstdbool>
#include <cstdint>
#include <ctgmath>
#include <cwchar>
#include <cwctype>
#endif

// C++
#include <algorithm>
#include <bitset>
#include <complex>
#include <deque>
#include <exception>
#include <fstream>
#include <functional>
#include <iomanip>
#include <ios>
#include <iosfwd>
#include <iostream>
#include <istream>
#include <iterator>
#include <limits>
#include <list>
#include <locale>
#include <map>
#include <memory>
#include <new>
#include <numeric>
#include <ostream>
#include <queue>
#include <set>
#include <sstream>
#include <stack>
#include <stdexcept>
#include <streambuf>
#include <string>
#include <typeinfo>
#include <utility>
#include <valarray>
#include <vector>

#if __cplusplus >= 201103L
#include <array>
#include <atomic>
#include <chrono>
#include <condition_variable>
#include <forward_list>
#include <future>
#include <initializer_list>
#include <mutex>
#include <random>
#include <ratio>
#include <regex>
#include <scoped_allocator>
#include <system_error>
#include <thread>
#include <tuple>
#include <typeindex>
#include <type_traits>
#include <unordered_map>
#include <unordered_set>
#endif
```

## Makefile
```makefile
fname=main
c:
	clang++ "$(fname).cpp" -o $(fname).out -std=c++17 -Wall -Wextra -Wshadow -Wfloat-equal -D_GLIBCXX_DEBUG -fsanitize=undefined -I/Users/cwza/cwza/cp/include
co: 
	clang++ "$(fname).cpp" -o $(fname).out -std=c++17 -O2 -I/Users/cwza/cwza/cp/include
cd: 
	clang++ "$(fname).cpp" --debug -o $(fname).out -std=c++17 -Wall -Wextra -Wshadow -Wfloat-equal -D_GLIBCXX_DEBUG -fsanitize=undefined -I/Users/cwza/cwza/cp/include
cg: 
	g++-13 "$(fname).cpp" -o $(fname).out -std=c++17 -Wall -Wextra -Wshadow -Wfloat-equal -D_GLIBCXX_DEBUG
cgo:
	g++-13 "$(fname).cpp" -o $(fname).out -std=c++17 -O2
r:
	./$(fname).out
```

## Config for Clangd
- brew intall bear
- `bear -- make` to generate compile_commands.json

-------------------------

# Linux C++

## Makefile
```makefile
c:
	g++ "$(fname).cpp" -o $(fname).out -std=c++17 -Wall -Wextra -Wshadow -Wfloat-equal -D_GLIBCXX_DEBUG -fsanitize=undefined
co: 
	g++ "$(fname).cpp" -o $(fname).out -std=c++17 -O2
cd: 
	clang++ "$(fname).cpp" --debug -o $(fname).out -std=c++17 -Wall -Wextra -Wshadow -Wfloat-equal -D_GLIBCXX_DEBUG -fsanitize=undefined
r:
	./$(fname).out
```


# Basic template for cp
```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;

void solve() {}

bool multi = 1;
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
