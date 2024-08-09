[[toc]]

# Linux Cpp Environment for Competitive Programming

## cp.sh
``` sh
g++ "${1:-main}.cpp" -o "${1:-main}" -std=c++17 -ggdb3 -Wall -Wextra -Wshadow -Wfloat-equal -D_GLIBCXX_DEBUG -D_GLIBCXX_ASSERTIONS -DDEBUG -fsanitize=undefined -fmax-errors=2
```

## Makefile
``` makefile
f=main
c: 
	g++ "$(f).cpp" -o "$(f)" -std=c++17 -Wall -Wextra -Wshadow -Wfloat-equal -D_GLIBCXX_DEBUG -D_GLIBCXX_ASSERTIONS -DDEBUG -fsanitize=undefined -fmax-errors=2
co:
	g++ "$(f).cpp" -o "$(f)" -std=c++17 -O2 -Wall -Wextra -Wshadow -Wfloat-equal -D_GLIBCXX_DEBUG -D_GLIBCXX_ASSERTIONS -DDEBUG -fsanitize=undefined -fmax-errors=2
cd: 
	g++ "$(f).cpp" -o "$(f)" -std=c++17 -ggdb3 -Wall -Wextra -Wshadow -Wfloat-equal -D_GLIBCXX_DEBUG -D_GLIBCXX_ASSERTIONS -DDEBUG -fsanitize=undefined -fmax-errors=2
r:
	./$(f)
```

## debug.hpp
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
    cerr << endl; \
}
```

## main.cpp
```cpp
#include <bits/stdc++.h>
using namespace std;
#ifdef DEBUG
    #include "debug.hpp"
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
