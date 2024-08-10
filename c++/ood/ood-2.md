# Reusing the implementation
+ `Composition`: construct new object with existing objects
  + Ways of inclusion:
    + Fully(Member)
    + By reference
  + It is the "has-a" relationship.
+ `Inheritance`: take the existing class, clone it and then make additions and modifications to the clone.
  + The ability to define the behavior or implementation of one class as a `superset` of another class
  + It is the "is-a" relationship
  + e.g Manager(Derived class, Sub, Child) -> Employee(Base class, Super, Parent)
  + 基本原则：所有成员变量均是private，接口是protected，对外的函数是public

# Function Overloading
Same functions with different arguments list
## Deafault Arguments
int harpo(int n, int m = 4, int j = 5);
int chico(int n, int m = 6, int j); // illegeal

## Overhead for a function call

the processing time required by a device prior to the execution of a command
+ Push parameters
+ Push return address
+ Prepare return values
+ Pop all pushed

## Inline Function
An inline function is expanded in place, like a preprocessor macro, so the overhead of a function call is eliminated.

Complier does the type check

+ Repeat `inline` keyword at both declaration and definition.
+ An inline function definition may not generate any code in .obj file.
```C++
inline int plusOne(int i);
inline int plusOne(int i) {
    i++;
}
```
+ 一个inline函数的definition是declaration，与其它函数不同。
+ 一般函数将declaration放在h文件，definition放在cpp文件
+ 但对于inline函数来说，将definition放在h文件即可，其作用与declaration相同

### Tradeoff of inline functions

+ Body of the called function is to be inserted into the caller.
+ This may expand the code size. 
+ but deduces the overhead of calling time.
+ So it gains speed at the expenses of space.
+ In most cases, it is worth.
+ It is much better than macro in C. It checks the types of the parameters.

### Notice
Inline may not inline The compiler does not have to honor your request to make a function inline. It may decide that the function is too large or notice that it calls itself (recursion is not allowed or actually possible with an inline function), or your particular compiler may not have implemented the feature.

### Inline Inside Classes

Any function you define inside a class declaration is automatically an inine.

### Inline Or not?
Inline when
+ Small functions, 2-3 lines
+ Frequently called functions, e.g. inside loops

Not inline when
+ Very large functions, more than 20 lines
+ Recursive functions

# Const

Constants are `variables`.
```C++
const int bufsize = 1024;
```
+ value `must` be `initialized`
+ unless you make an explicit extern declaration:
  + Compiler won't let you change it
    ```C++
    extern const int bufsize;
    ```
+ `Compile time constants` are entries in compiler symbol table, not really variables (Using #define)

## Run-time constants
const value can be exploited
```C++
const int class_size = 12;
int finalGrade[class_size]; // ok

int x;
cin >> x;
const int size = x;
double classAverage[size]; // error!
```
## Pointers and const

aPointer -- may be const
aValue -- may be const
```C++
char * const q = "abc";  // q is const
*q = 'c';  // OK
q++;  // ERROR

const char *p = "ABCD";
// (*p) is a const char
*p = 'b';  // ERROR! (*p) is the const
```
Exercise:
```C++
Pereson p1("Fred". 200);
const Person* p = &p1;//对象是const
Person const* p = &p1;//看*号位置，const在*号前面就是对象是const
Person *const p = &p1;//指针是const


|                | int i;       | const int ci = 3;    |
|----------------|--------------|----------------------|
| int * ip;      | ip = &i;     | ip = &ci;  // Error  |
| const int *cip | cip = &i;    | cip = &ci;           |

Remember:
*ip = 54;   // always legal since ip points to int
*cip = 54;  // never legal since cip points to const int
```
## const member function
Cannot modify their objects.
```C++
int Date::set_day(int d) {
    // ...error check d here...
    day = d;        // ok, non-const so can modify
}

int Date::get_day() const {
    day++;          // ERROR modifies data member
    set_day(12);    // ERROR calls non-const member
    return day;     // ok
}
```
Its usage:
+ Repeat the `const` keyword int the definition as well as the declaration
  + int get_day() const;
  + int get_day() const {return day};
  + Actually it means that `*this` is constant
```C++
 int Date::set_day(int d) {
    day = d;//works fine
  }
 int Date::set_day() const {
    day++; //Error modifies data member
    set_day(12); //Error calls non-const member
  }
```
+ Function members that do not modify data should be declared `const`
+ const member functions are safe for const object

Another Example:
```C++
#include <iostream>
using namespace std;

class A {
    int i;
public:
    A() : i(0) {}
    void f() { cout << "f()" << endl; }
    void f() const { cout << "f() const" << endl; }
};

int main() {
    const A a;
    a.f();

    return 0;
}
```
Two f() here can exsit since you can turn it into this:
```C++
    void f(A* this) { cout << "f()" << endl; }
    void f(const A* this) const { cout << "f() const" << endl; }
```