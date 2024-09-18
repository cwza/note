[toc]

# Linux Cpp Environment for Competitive Programming

## Competitive Programming Tool Shell Script
* Easy to compile cpp file
* `./tool/cpt.sh`
* Better to put this file to your system path 
``` sh
#!/bin/bash

optimize=0
while getopts "oI:" flag
do
    case "${flag}" in
        o) optimize=1;;
    esac
done
filename=${@:$OPTIND:1} 
if [ -z $filename ]; then
    filename=main
fi
# echo "optimize=$optimize"
# echo "filename=$filename"

if [ $optimize -eq 0 ]; then
    g++ "$filename.cpp" -o "$filename" -std=c++17 -Wall -Wextra -Wshadow -Wfloat-equal -D_GLIBCXX_DEBUG -D_GLIBCXX_ASSERTIONS -DDEBUG -fsanitize=undefined -fmax-errors=2
else
    g++ "$filename.cpp" -o "$filename" -std=c++17 -O2 -DDEBUG -fmax-errors=2
fi
```

## Competitive Programming Debug Tool
* Easy to print something for debugging
* `./tool/debug.hpp`
``` cpp
#include <bits/stdc++.h>
using namespace std;

// pretty cout pair
template<typename Ostream, typename ...Ts>
Ostream& operator<<(Ostream& os,  const pair<Ts...>& p){
	return os<<"("<<p.first<<", "<<p.second<<")";
}

// pretty cout tuple
template<typename Ostream, class TupType, size_t... I>
void print_tuple(Ostream& os, const TupType& _tup, index_sequence<I...>) {
    (..., (os << (I == 0? "" : ", ") << get<I>(_tup)));
}
template<typename Ostream, class... T>
Ostream& operator<<(Ostream& os, const std::tuple<T...>& _tup) {
    os << "(";
    print_tuple(os, _tup, std::make_index_sequence<sizeof...(T)>());
    os << ")";
    return os;
}

// pretty cout container
template<typename Ostream, typename Cont>
enable_if_t<is_same_v<Ostream,ostream>, Ostream&> operator<<(Ostream& os,  const Cont& v) {
	os<<"[";
	for(auto& x:v){os<<x<<", ";}
	return os<<"]";
}

// pretty cout variadic arguments
template <typename Ostream, typename... Args>
void print_args(Ostream& os, Args... args) {
    (..., (os << args << ", "));
}


#define dbg(...) { \
    cerr <<"\e[91m" << __func__<<":"<<__LINE__<<"\t"; \
    if(std::tuple_size<decltype(std::make_tuple(__VA_ARGS__))>::value==1) { \
        cerr << #__VA_ARGS__ << ": "; \
        print_args(cerr, __VA_ARGS__); \
    } else { \
        cerr << "{" << #__VA_ARGS__ << "}: "; \
        cerr << "{"; \
        print_args(cerr, __VA_ARGS__); \
        cerr << "}"; \
    } \
    cerr << "\e[39m" << endl; \
}
```

## Competitive Programming Simple Template
* `./main.cpp`
```cpp
#include <bits/stdc++.h>
using namespace std;
#ifdef DEBUG
    #include "./tool/debug.hpp"
#else
    #define dbg(...)
#endif
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
