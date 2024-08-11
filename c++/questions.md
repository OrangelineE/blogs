# Questions

1. What is the difference between C++ and C?

    C: Procedural language, no object-oriented features, uses malloc/free for memory management.

    C++: Multi-paradigm (supports both procedural and object-oriented programming), includes classes, inheritance, function overloading, and uses new/delete for memory management.


2. Does the processing of #define macro replacement occur during the compilation or pre-compilation phase?

    #define Macro Replacement happens during the `pre-compilation` phase (preprocessing), before the actual compilation begins.

3. What is the difference between compilation and pre-compilation?

    Compilation: Translates the entire source code into machine code before execution, resulting in faster execution (e.g., C, C++).

    Pre-compilation: The preprocessing step that occurs before the main compilation phase. It handles code modification based on directives before the actual compilation begins. It is mainly about text substitution and conditional code inclusion.

4. What are the three main features of object-oriented programming?
    1. Encapsulation:
    
        Definition: Encapsulation is the concept of bundling data (attributes) and methods (functions) that operate on the data into a single unit called a class. It also involves restricting access to certain details of an object, which can only be accessed through public methods, providing a controlled interface.

        Benefit: Encapsulation helps protect the internal state of an object from unintended interference and misuse, promoting data integrity and security.

    2. Inheritance:
    
        Definition: Inheritance is a mechanism that allows a new class (derived class) to inherit properties and behaviors (methods) from an existing class (base class). The derived class can extend or override the inherited features.

        Benefit: Inheritance promotes code reusability, as common functionality can be defined in a base class and shared across multiple derived classes, reducing redundancy.

    3. Polymorphism:

        Definition: Polymorphism allows objects of different classes to be treated as objects of a common base class. The most common types of polymorphism are method overriding (runtime polymorphism) and method overloading (compile-time polymorphism).

        Benefit: Polymorphism enables flexibility and scalability in code, allowing for a unified interface while supporting different underlying implementations, making the code easier to extend and maintain.

5. What is the difference between using angle brackets < > and double quotes "" in include statements?

    < >: Used for `standard/system` headers; searches in system directories.

    "": Used for `local` or `user-defined` headers; searches in the current directory first, then in system directories if not found.

6. What are the scope and lifetime of member variables, and what is the difference between global variables?
    
    1. Member Variables:

        Scope: The scope of member variables (also known as instance variables) is within the class they belong to. They can be accessed by any methods of the class and are typically private, protected, or public, depending on the access specifier.

        Lifetime: The lifetime of a member variable is tied to the lifetime of the object instance. When an object is created, its member variables are instantiated. When the object is destroyed, the member variables are also destroyed.

    2. Global Variables:

        Scope: The scope of a global variable is throughout the entire program. It is accessible from any function or method in the program after it is declared.

        Lifetime: The lifetime of a global variable is the entire duration of the program's execution. It is created when the program starts and destroyed when the program ends.

7. What is the purpose of extern?

    1. Linking External Variables:
        
        When you want to use a global variable defined in another file, you can use the extern keyword to declare that variable in the current file. This tells the compiler that the variable exists and is defined elsewhere.
    2. Avoiding Multiple Definitions:
    
        `extern` allows you to declare a variable or function without defining it, avoiding multiple definitions in different files. The actual definition should be in a single file, and other files can declare it with extern.

8. Can you distinguish between default constructors, copy constructors, assignment operators, and conversion operators?

    1. Default Constructor

        Definition: A default constructor is a constructor that can be called with no arguments. It initializes objects of a class with default values.

        Purpose: To create an object without requiring initial values for its members.

        Automatic Generation: If no constructors are explicitly defined, the compiler automatically provides a default constructor.
 
        ```C++
        class MyClass {
        public:
            MyClass() { // Default constructor
                // Initialize members to default values
            }
        };

        MyClass obj; // Calls the default constructor
        ```
    2. Copy Constructor
    
        Definition: A copy constructor is a constructor that initializes an object using another object of the same class.

        Purpose: To create a new object as a copy of an existing object.

        Automatic Generation: The compiler provides a default copy constructor if none is defined, which performs a shallow copy.

        ```C++
            class MyClass {
            public:
                MyClass(const MyClass &other) { // Copy constructor
                    // Copy data from 'other' to this object
                }
            };

            MyClass obj1;
            MyClass obj2 = obj1; // Calls the copy constructor
        ```
    3. Assignment Operator
        
        Definition: The assignment operator (operator=) is used to assign the contents of one existing object to another existing object of the same class.

        Purpose: To copy the state of one object to another after both objects have been constructed.

        Automatic Generation: The compiler provides a default assignment operator if none is defined, which performs a shallow copy.

        ```C++
        class MyClass {
        public:
            MyClass& operator=(const MyClass &other) { // Assignment operator
                if (this != &other) {
                    // Copy data from 'other' to this object
                }
                return *this;
            }
        };

        MyClass obj1, obj2;
        obj2 = obj1; // Calls the assignment operator
        ```
    4. Conversion Operator

        Definition: A conversion operator is a special operator that allows an object of a class to be implicitly converted to another type.

        Purpose: To define how an object of a class can be converted to a different data type.

        Syntax: Conversion operators are defined using the operator keyword followed by the target type.
        ```C++
        class MyClass {
        public:
            operator int() const { // Conversion operator to int
                return someValue;
            }
        private:
            int someValue;
        };

        MyClass obj;
        int x = obj; // Implicitly calls the conversion operator
        ```
9. What is the difference between new & delete and malloc & free?
    + new & delete:
        1. C++ operators.
        2. Type-safe, calls constructors/destructors, initializes memory, and supports overloading.
        3. Throws exceptions on failure.
    + malloc & free:
        1. C functions.
        2. Requires manual type casting, does not call constructors/destructors, does not initialize memory by default, and cannot be overloaded.
        3. Returns NULL on failure.


10. What is the difference between malloc and calloc?

    + malloc:
        1. Allocates memory without initializing it.
        2. Takes one argument: the total number of bytes to allocate.
        3. Faster but may require manual initialization.
        ```C++
        int *ptr = (int*)malloc(5 * sizeof(int));  // Allocates memory for 5 integers, uninitialized
        ```
    + calloc:
        1. Allocates memory and initializes it to zero.
        2. Takes two arguments: the number of elements and the size of each element.
        3. Slightly slower due to initialization but ensures that the memory is zeroed out.
        ```C++
        int *ptr = (int*)calloc(5, sizeof(int));  // Allocates memory for 5 integers, initialized to zero
        ```

11. Explain the roles of public, private, and protected.
    + Public:
        
        Access: Accessible from anywhere (inside the class, outside the class, in derived classes).

        Usage: For members that should be part of the class's interface and accessible to all.
    + Private:
        
        Access: Accessible only within the class that declares them.

        Usage: For members that should be hidden from outside access and are only meant for internal use within the class.
    + Protected:
        
        Access: Accessible within the class and its derived classes, but not by outside functions or objects.

        Usage: For members that should be inherited by subclasses but not accessible to the rest of the program.
12. What is the difference between initialization list initialization and constructor body initialization?

    + Initialization Timing:

        Initialization List: Initializes members before the constructor body is executed. The members are directly constructed with the given values.

        Constructor Body: Initializes members after they have been default-initialized or uninitialized. Assigns values in the constructor body.

    + Efficiency:

        Initialization List: More efficient as it avoids redundant initializations. Directly constructs objects with the desired state.

        Constructor Body: Less efficient, especially for non-primitive types, as it may involve default construction followed by assignment.

    + Usability:

        Initialization List: Required for initializing const members, references, and objects that do not have a default constructor.

        Constructor Body: Suitable for primitive types but not recommended for complex types or where efficiency is critical.

13. What is the difference between public inheritance and private inheritance?
14. What is the difference between overload and override?
15. Does C support function overloading? Why or why not?

    C does not support function overloading due to its lack of name mangling and the simplicity of its function naming system.

    C++ introduced function overloading to provide more flexibility and support for object-oriented programming.
    
    In C, similar functionality can be achieved through alternative methods such as using different function names, void*, or variadic functions, but these methods do not provide the same level of convenience or type safety as true function overloading.

16. What are the advantages and disadvantages of inline functions?
17. When is it appropriate to use inline functions?
18. What is the difference between const and macro replacement?
19. What is the difference between constant pointers and pointers to constants?
20. What is the difference between references and pointers?
21. Do you understand the four types of type conversions in C++? Can you give examples?
22. What is the role of static? What is the scope and lifetime of static variables?


