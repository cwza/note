# Order Statistic Tree in GCC
- O(logN)

```cpp
#include <ext/pb_ds/assoc_container.hpp>
using namespace __gnu_pbds;
using index_set = tree<int,null_type,less<int>,rb_tree_tag,tree_order_statistics_node_update>;

int main() {
    index_set X;
    X.insert(1);
    X.insert(2);
    X.insert(4);
    X.insert(8);
    X.insert(16);

    // find the k+1th
    cout<<*X.find_by_order(1)<<endl; // 2
    cout<<*X.find_by_order(2)<<endl; // 4
    cout<<*X.find_by_order(4)<<endl; // 16
    cout<<(end(X)==X.find_by_order(6))<<endl; // true

    // find the number which < k
    cout<<X.order_of_key(-5)<<endl;  // 0
    cout<<X.order_of_key(1)<<endl;   // 0
    cout<<X.order_of_key(3)<<endl;   // 2
    cout<<X.order_of_key(4)<<endl;   // 2
    cout<<X.order_of_key(400)<<endl; // 5
}
```
