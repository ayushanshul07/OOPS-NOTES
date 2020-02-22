#                                 `Constructors`
---

> A **constructor** is a special kind of class member function that is automatically called when an object of that class is instantiated. Constructors are typically used to initialize member variables of the class to appropriate default or user-provided values, or to do any setup steps necessary for the class to be used (e.g. open a file or database).

> Constructors must have the same name as the class (with the same capitalization). Constructors have no return type (not even void).


## `Default Constructors`

> A constructor that takes no parameters (or has parameters that all have default values) is called a default constructor. The default constructor is called if no user-provided initialization values are provided.

> **Rule**: Use uniform initialization to initialize class objects

> **Rule**: Do not copy initialize your classes

> If your class has no other constructors, C++ will automatically generate a public default constructor for you. This is sometimes called an **implicit constructor** (or implicitly generated constructor).This particular implicit constructor allows us to create an object with no parameters, but doesn’t initialize any of the members (because all of the members are fundamental types, and those don’t get initialized upon creation). If your class has any other constructors, the implicitly generated constructor will not be provided. 

> **Rule**: Provide at least one constructor for your class, even if it’s an empty default constructor.

> A class may contain other classes as member variables. By default, when the outer class is constructed, the member variables will have their default constructors called. This happens before the body of the constructor executes.

```c
#include <iostream>
 
class A
{
public:
    A() { std::cout << "A\n"; }
};
 
class B
{
private:
    A m_a; // B contains A as a member variable
 
public:
    B() { std::cout << "B\n"; }
};
 
int main()
{
    B b;
    return 0;
}


This prints:
A
B
```

> **Best practice**: Always initialize all member variables in your objects.


## `Member initializer lists`

> **Rule**: Use member initializer lists to initialize your class member variables instead of assignment.

> **Rule**: Favor uniform initialization over direct initialization if your compiler is C++11 compatible


## `Non-static Member Initialization`

> Starting with C++11, it’s possible to give normal class member variables (those that don’t use the static keyword) a default initialization value directly:

```c
class Rectangle
{
private:
    double m_length = 1.0; // m_length has a default value of 1.0
    double m_width = 1.0; // m_width has a default value of 1.0
 
public:
    Rectangle()
    {
    // This constructor will use the default values above since they aren't overridden here
    }
 
    void print()
    {
        std::cout << "length: " << m_length << ", width: " << m_width << '\n';
    }
};
 
int main()
{
    Rectangle x; // x.m_length = 1.0, x.m_width = 1.0
    x.print();
 
    return 0;
}

This program produces the result:
length: 1.0, width: 1.0
```

> Non-static member initialization (also called in-class member initializers) provides default values for your member variables that your constructors will use if the constructors do not provide initialization values for the members themselves (via the member initialization list). However, note that constructors still determine what kind of objects may be created. Consider the following case:

```c
class Rectangle
{
private:
    double m_length = 1.0;
    double m_width = 1.0;
 
public:
 
    // note: No default constructor provided in this example
 
    Rectangle(double length, double width)
        : m_length(length), m_width(width)
    {
        // m_length and m_width are initialized by the constructor (the default values aren't used)
    }
 
    void print()
    {
        std::cout << "length: " << m_length << ", width: " << m_width << '\n';
    }
 
};
 
int main()
{
    Rectangle x; // will not compile, no default constructor exists, even though members have default initialization values
 
    return 0;
}
```

> If a default initialization value is provided and the constructor initializes the member via the member initializer list, the member initializer list will take precedence.

> Note that initializing members using non-static member initialization requires using either an equals sign, or a brace (uniform) initializer -- the direct initialization form doesn’t work here.

> **Rule**: Favor use of non-static member initialization to give default values for your member variables.

