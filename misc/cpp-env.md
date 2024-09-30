[toc]

# Linux Cpp Environment for Competitive Programming

## Competitive Programming Tool Shell Script
* Easy to compile cpp file
* `./tool/cpt`
* Better to put this file to your system path 
``` sh
#!/bin/bash

optimize=0
version=17
while getopts "oI:v:" flag
do
    case "${flag}" in
        o) optimize=1;;
        v) version=$OPTARG;;
    esac
done
filename=${@:$OPTIND:1} 
if [ -z $filename ]; then
    filename=main
fi
# echo "optimize=$optimize"
# echo "filename=$filename"
# echo "version=$version"

if [ $optimize -eq 0 ]; then
    g++ "$filename.cpp" -o "$filename" -std=c++$version -Wall -Wextra -Wshadow -Wfloat-equal -D_GLIBCXX_DEBUG -D_GLIBCXX_ASSERTIONS -DDEBUG -fsanitize=undefined -fmax-errors=2
else
    g++ "$filename.cpp" -o "$filename" -std=c++$version -O2 -fmax-errors=2
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
void print_tuples(Ostream& os, const TupType& _tup, index_sequence<I...>) {
    (..., (os << (I == 0? "" : ", ") << get<I>(_tup)));
}
template<typename Ostream, class... T>
Ostream& operator<<(Ostream& os, const std::tuple<T...>& _tup) {
    os << "(";
    print_tuples(os, _tup, std::make_index_sequence<sizeof...(T)>());
    os << ")";
    return os;
}

// pretty cout container
template<typename Ostream, typename Cont>
Ostream& operator<<(Ostream& os,  const Cont& v) {
	os<<"[";
	for(auto& x:v){os<<x<<", ";}
	return os<<"]";
}

// pretty cout variadic arguments
template<typename Ostream, typename... Args>
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

// pretty cout array
template <typename T>
std::enable_if_t<rank<T>::value == 1>
print_arr(const T& a) {
    for(size_t i = 0; i < extent<T>::value; i++) cerr << a[i] << " ";
}
 
template <typename T>
std::enable_if_t<rank<T>::value != 1>
print_arr(const T& a)
{
    for(size_t i = 0; i < extent<T>::value; i++) {
        print_arr(a[i]);
        if(i!=extent<T>::value-1)
            for(size_t j = 0; j < rank<T>::value-1; j++) cerr << "\n";
    }
}
#define dbgarr(a) { \
    cerr <<"\e[91m" << __func__<<":"<<__LINE__<<"\t"; \
    cerr << #a << ": \n"; \
    print_arr(a); \
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
    #define dbgarr(a)
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
