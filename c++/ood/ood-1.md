# OOP
## What is Object?
+ Attributes (Data: the properties or status) 
+ Services (Operations: the functions)

Solving a problem is actually mapping the problem space to the solution space

## What is object-oriented
+ A way to orgranize 
  + Designs
  + Implementations
+ Objects, not control or data flow, are the primary focus of the design and implementation
+ To focus on things, not operations

## OOP Characteristics
1. Everything is an object.
2. A program is a bunch of objects telling each other `what to do` by sending messages.
3. Each object has its own memory made up of other objects.
4. Every object has a type.
5. All objects of a particular type can receive the same messages. 

## Interface
An object has an interface
+ The interface is the way it receives messages
+ It is defined in the class the object belong to
### Functions of interface
+ Communication
+ Protection

## Encapsulation
+ bundle data and methods dealing with data together
+ Hide the details of the data and the action
+ Restrict only accesss to the publicized methods

## Resolver ::
<class name> :: <function name>

## Header
### Definition of a class
+ In C++, seperated .h and .cpp files are used to define one class
+ Class `declaration` and `prototypes` in that class are in the `header` file(.h)
+ All the bodies of these functinos are in the source file(.cpp)

### Header = Interface

+ Header is the contract between you and the user of your code.
+ The compile enforces the contract by requiring you to declare all the structures and functions before they are used.

### Declarations vs Definitions
+ A .cpp file is a compile unit
+ Only declarations are allowed to be in .h
  + `extern` variables
  + function prototypes (Without `{}`)
  + class/struct declarations

### `#include`
+ #include "xx.h": first search in the current directory, then the directories declared somewhere
+ #include <xx.h>: search in the specified directories
+ #include <xx>: same as #include <xx.h>

### Tips
1. One class declaration per header file
2. Associated with one source file in the same prefix of file name
3. The contents of a header file is surrounded with #ifndef, #define, #endif.
   
## Fields, Parameters, Local Variables
+ Fields are defined outside constructors and methods
  + Used to store data that persists throughout the life of an object.
  + Have class scope: their accessibility extends throughout the whole class and so they can be used within any of the constructors or methods of the class in which they are defined.

## this - the hidden parameter
`this` is a hidden parameter for all member functions with the type of the class

```C++
Point a;
a.print(); 

equals to 

Point::print(&a);
```

# Constructor & Deconstructor

1. The constructor:
  +  Default Constuctor (No arguments)
  ```C++
  class X {
    private:
    int i;
    public:
    X();
  }
  ```
  + Constructor with argument
    + Copy constructor (create a new object from an existing one)
    
1. The deconstructor
  ```C++
  class X {
    public:
    ~X();
  }
  ```

# Dynamic Memory Allocation

## new
+ new int
+ new Stash
+ new int[10]
  + The new operator returns the address of the first element of the block
## delete
+ delete [] psome;
  + The presence of the brackets tells the programm that it should free the whole array, not just the element

### Tips
1. use delete[] if you used new[]
2. use delete (no brackets) if you use new
3. It's safe to apply delete to the null pointer
4. Don't use delete to free memory that new didn't allocare
5. Don't use delete to free the same block of memory twice in succession

# C++ Access Control
+ `private`
  + The class members declared as private can be accessed only by the member functions inside the class.
  + They are not allowed to be accessed directly by any object or function outside the class. 
  + Only the `member` functions or the `friend` functions are allowed to access the private data members of the class. 
+ `public`
  + All the class members declared under the public specifier will be available to everyone. 
  + The data members and member functions declared as public can be accessed by other classes and functions too. 
  + The public members of a class can be accessed from anywhere in the program using the direct member access operator (.) with the object of that class. 
+ `protected`
  + Similar to the private access modifier in the sense that it can’t be accessed outside of its class unless with the help of a friend class. 
  + The difference is that the class members declared as Protected can be accessed by any `subclass` (derived class) of that class as well. 
## Friend
+ To `explicitly` grant access to a function that is not a member of the structure
+ The class itself controls which code has access to its members.
+ Can declare a global function as a frined, as well as a member function of another class or even an entire class as a friend.

## class vs struct
+ class: default as private
+ struct: default as public

# Initialized List
The list of members to be initialized is indicated with constructor as a comma-separated list followed by a colon.
```C++
#include <iostream>
using namespace std;
 
class Point {
private:
    int x;
    int y;
 
public:
    Point(int i = 0, int j = 0): x(i), y(j) {}
    /*  The above use of Initializer list is optional as the
        constructor can also be written as:
        Point(int i = 0, int j = 0) {
            x = i;
            y = j;
        }
    */
 
    int getX() const { return x; }
    int getY() const { return y; }
};
 
int main()
{
    Point t1(10, 15);
    cout << "x = " << t1.getX() << ", ";
    cout << "y = " << t1.getY();
    return 0;
}
```

## Initialization vs Assignment
+ Initialization:
  + before constructor
  ```C++
  Student::Student(string s):name(s) {}
  ```
+ Assignment
  + Inside constuctor
  ```C++
  Student::Student(string s):name(s) {}
  ```