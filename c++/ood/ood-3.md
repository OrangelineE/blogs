# Reference
## Declaring references

References are a new data type in C++
```C++
    char c; // a character
    char* p = &c; // a pointer to a character
    char& r = c; // a reference to a character
```
  
+ Local or global variables
  + type& refname = name;
  + For ordinary variables, the `initial` value is `required`
+ In parameter lists and member variables
  + type& refname
  + Binding defined by caller or constructor
## Restrinctions
+ No references to references
+ No pointers to references
  + int&* p; // illegal, 离变量最近的决定其类型
+ Reference to pointer is ok
  + void f(int*& p);
+ No arrays of references

## Upcasting
Upcasting is the act of converting from a Derived reference or pointer to a base class reference or pointer.

## Polymorphism 
Dynamic Binding
### Virtual Keyword
A virtual function (also known as virtual methods) is a member function that is declared within a base class and is re-defined (overridden) by a derived class. When you refer to a derived class object using a pointer or a reference to the base class, you can call a virtual function for that object and execute the derived class’s version of the method.

+ Virtual functions ensure that the correct function is called for an object, regardless of the type of reference (or pointer) used for the function call.
+ They are mainly used to achieve Runtime polymorphism.
+ Functions are declared with a virtual keyword in a base class.
+ The resolving of a function call is done at runtime.

`Make destructors virtual if they might be inherited`

## Override
Overriding redefines the body of a virtual function

# Static
Two basic meanings
+ Static Storage
  + allocated once at a fixed address
+ Visibility of a name
  + internal linkage
+ Don't use static except inside functions and classes

## Usage

1. Static local variables - Persistent storage
2. Static member variables - Shared by all instances
3. Static member functions - Shared by all instances, can only access static member variables

### Static inside a function
+ Value is remembered for entire program
+ Initialization occurs only once.
  + Construction occurs when definition is encountered
    + Constructor called at-most once
    + The constuctor arguments must be satisfied
    + Constructors are called before main() is entered
      + Order controlled by apperance in file
      + main() is no longer the first function called
  + Destruction takes place on exit from program
    + Compiler assures LIFO order of destructors
    + Destructors is called when
      + main() exits
      + exit() is called
  
### Static Members
```C++
class A {
  public:
    A(): {
      i = 0;
    }
    void print() {cout << i << endl;}
    void set(int ii) { i = ii; }
  private: static int i; //It's not definition, it's only declaration
}

int A::i = 20; //This is required and it acts as definition.

int main() {
  A a,b;
  a.set(10); 
  b.print(); //without line 82, it even can not compile since the compiler don't know where to find `i` and it only knows there exits `i`.
  //with line 82, it print `0`.
  return 0;
}
```
`Btw, the static member can not be intialized through initialization lists.` The initialized list can only initialize non-static members.

Static members don't have `*this`.

### Static Functions
Static functions don't have `*this`. Therefore, it can not access non-static members or non-static functions.

## Overload

### Overloading Rules
+ check first for unique function match
+ Then check for unique function template match
+ Then do overloading on functions
### Operators that can be Overloaded in C++

+ Arithmetic Operators: +, -, *, /, %
+ Relational Operators: ==, !=, <, >, <=, >=
+ Logical Operators: &&, ||, !
+ Bitwise Operators: &, |, ^, ~, <<, >>
+ Assignment Operators: =, +=, -=, *=, /=, %= &=, |=, ^=, <<=, >>=
+ Increment and Decrement Operators: ++, --
+ Function Call Operator: ()
+ Subscript Operator: []
+ Member Access Operators: ->, .*, ->*
+ Dereference and Address-of Operators: *, &
+ Unary Operators: +, -, *, &, !, ~
+ New and Delete Operators: new, new[], delete, delete[]
+ Type-cast Operator: ()
+ Comma Operator: ,
+ Comparison Operator: <=> (C++20)

Some operators cannot be overloaded. These include:

+ Scope resolution operator ::
+ Member access through pointer to member operator .*
+ Sizeof operator sizeof
+ Conditional (ternary) operator ?:
+ Typeid operator typeid
+ Alignof operator alignof
+ Noexcept operator noexcept

### How to overload
+ As member function (operator *())
  + Implicit first argument (*this)
  + No type conversion performed on receiver
  + Must have access to class definition
  ```C++
  class Integer{
    public:
      Integer(int n = 0): i(n) {}
      const Integer operator+（const Integer& n) const {
        return Integer(i + n.i);
      }
      ...
      private:
      int i;
  }
  ```
  Assume Integer x(1), y(5), z

  x + y -> the operand on the left of operator+ is called `receiver`.

  No type conversion performed on receiver.
  + OK: z = x + y
  + OK: z = x + 3
  + NO: z = 3 + y
  + NO: z = x + 3.5
+ Operator as a global function
  + Explicit first argument
  + Developer doesn't need special access to classes
  + May need to be a friend
  + Type conversions performed on both arguments
    + OK: z = x + y
    + OK: z = x + 3
    + OK: z = 3 + y
    + OK: z = 3 + 7
### Tips
+ Unary operators should be members
+ = () [] -> ->* must be members
+ assignment operators should be members
+ All other binary operators as non-members

### Argument Passing
+ If it is read-only pass it in as a const reference (except built-ins).
+ Make member functions const that don't change the class (boolean operators, +, -, etc).
+ For global functions, if the left-hand side changes, pass as a reference (assignment operators).
  
### The prototypes of operators

+ +-*/%^&|~
  + const T operatorX(const T& l, const T& r) const;
+ ! && || < <= == >= >
  + bool operatorX(const T& l, const T& r) const;
+ []
  + T& T::operator[](int index);
  + 
### Operators ++ and --

How to distinguish postfix from prefix?

Postfix forms take an int argument — the compiler will pass in 0 as that int.
```C++
class Integer {
public:
    ...
    const Integer& operator++();     // prefix ++ ---> ++a (the result after the addition which is a)
    const Integer operator++(int);   // postfix ++ ---> a++ (the result before the addition)
    const Integer& operator--();     // prefix --
    const Integer operator--(int);   // postfix --
    ...
};

const Integer& Integer::operator++() {
    *this += 1;        // increment
    return *this;      // fetch
}

// int argument not used so leave unnamed so 
// won't get compiler warnings
//x++ calls x.operator++(0);
const Integer Integer::operator++(int) {
    Integer old(*this); // fetch
    ++(*this);          // increment
    return old;         // return
}

```

### Relational Operators
```C++
bool Integer::operator==(const Integer& rhs) const {
    return i == rhs.i;
}

// implement lhs != rhs in terms of !(lhs == rhs)
bool Integer::operator!=(const Integer& rhs) const {
    return !(*this == rhs);
}

bool Integer::operator<(const Integer& rhs) const {
    return i < rhs.i;
}

// implement lhs > rhs in terms of lhs < rhs
bool Integer::operator>(const Integer& rhs) const {
    return rhs < *this;
}

// implement lhs <= rhs in terms of !(rhs < lhs)
bool Integer::operator<=(const Integer& rhs) const {
    return !(rhs < *this);
}

// implement lhs >= rhs in terms of !(lhs < rhs)
bool Integer::operator>=(const Integer& rhs) const {
    return !(*this < rhs);
}

```
使用已有函数的好处：当有改动时，只需要修改一个函数，剩下的可自动改好

### Operator []
+ Must be a member function with single argument
### Assignment Operator
```C++
T& T::operator=(const T& rhs) {
    // check for self-assignment
    if (this != &rhs) {
        // perform assignment
    }
    return *this;
}

// This checks address vs. check value (*this != rhs)
// This prevents unnecessary operations and potential issues like double deletion in case of dynamic memory.
```
+ For classes with dynamically allocated memory, declare an assignment operator (and a copy constructor).
+ To prevent assignment, explicitly declare operator= as private.

### User-defined Type Conversions

+ A conversion operator can be used to convert an object of one class into:
  + an object of another class
  + a built-in type
+ Compilers perform implicit conversions using:
  + Single-argument constructors
  + Implicit type conversion operators
  + 
  ```C++
  class PathName {
    string name;
  public:
      // or could be multi-argument with defaults
      PathName(const string&);
      ~PathName();
  };
  ...
  string abc("abc");
  PathName xyz(abc); // OK!
  xyz = abc;         // OK abc => PathName
  ```

How to prevent Implicit Conversions?
  
  ```C++
    explicit PathName(const string&);
  ```
### General form of conversion ops

X::operator T()
+ Operator name is any type descriptor.
+ No explicit arguments.
+ No return type.
+ Compiler will use it as a type conversion from X to T.
### C++ type conversions

+ Built-in conversions

  + Primitive
    + char ⇒ short ⇒ int ⇒ float ⇒ double ⇒ int ⇒ long
  + Implicit (for any type T)
    + T ⇒ T&
    + T& ⇒ T
    + T* ⇒ void*
    + T[] ⇒ T*
    + T* ⇒ T[]
    + T ⇒ const T
+ User-defined T ⇒ C
  + If C(T) is a valid constructor call for C
  + If operator C() is defined for T