# Template Classes and Methods
## Example
Template classes are useful for writing containers.
```cpp
template <typename T>
class list {
public:
  list(T data) : data_(data) {}
  T data() const { return data_; }
private:
  T data_;
  list<T>* next_;
};
```
# Parameter Packs
Parameter packs are useful when your template class or template function can accept various different types.

## Tuple
Here's a simple tuple wrapper.
```cpp
#include <iostream>
#include <tuple>
using namespace std;

template <typename... Ts>
class Tuple {
public:
  Tuple(tuple<Ts...> data) : data_(data) {}
  tuple<Ts...> data() { return data_; }
private:
  tuple<Ts...> data_;
};

int main() {
  Tuple<int> t = make_tuple(3);
  cout << get<0>(t.data()) << "\n";
}
```
Output:
```
3
```
# `constexpr if` Statements
# Fold Expressions
# Structured Bindings
# Universal References
# SFINAE
# `any`, `optional`, `tuple`
# `unique_ptr`
# `shared_ptr`
