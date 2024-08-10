# Templates

```C++
//Just declaration (compiler just remember it)
template <class T>
void swap(T& x, T& y) {
    T temp = x;
    x = y;
    y = temp;
}

int main() {
    int i = 3; int j = 4;
    swap(i, j); // when you call the template function, the compiler will run it automatically based on your templates.
}
```

Usage:
+ functions
+ classes
## Function Instantiation

The compiler deduces the template type from the actual arguments passed into the function.

Can be explicit:
+ For example, if the parameter is not in the function signature (older compilers won't allow this...)
  
    ```C++
    template <class T>
    void foo(void) {
        /* ... */
    }

    foo<int>();    // type T is int
    foo<float>();  // type T is float
    ```
`Templates can use multiple types`
```C++
template<calss Key, class Value>
class HashTable {
    const Value& lookup(const Key&) const;
    void install(const Key&, const Value&)
}
```
`Templates can use non-type paramemters`
+ non-type parameters can have default argument
+ 
    ```C++
    template <class T, int bounds = 100>
    class FixedVector {
    public:
        FixedVector();
        // ...
        T& operator[](int);
        
    private:
        T elements[bounds]; // fixed size array!
    };
    ```
    + FixedVector<int, 50> v1;
    + FixedVector<int> v3;