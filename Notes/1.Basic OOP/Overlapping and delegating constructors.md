#        Overlapping and delegating constructors
---


## `Constructors with overlapping functionality`

```c
class Foo
{
public:
    Foo()
    {
        // code to do A
    }
 
    Foo(int value)
    {
        // code to do A
        // code to do B
    }
};
```


> Prior to C++11, calling a constructor explicitly from another constructor creates a temporary object, initializes the temporary object using the constructor, and then discards it, leaving your original object unchanged

```c
class Foo
{
public:
    Foo()
    {
        // code to do A
    }
 
    Foo(int value)
    {
        Foo(); // use the above constructor to do A (doesn't work)
        // code to do B
    }
};
```

or
	
```c
class Foo
{
public:
    Foo()
    {
        // code to do A
    }
 
    Foo(int value): Foo() // use the above constructor to do A (doesn't work prior to C++11)
    {
        // code to do B
    }
};
```


>  The best solution in this case was to move the code from the constructor to your new function, and have the constructor call your function to do the work of initializing the data:

```c
class Foo
{
public:
    Foo()
    {
        Init();
    }
 
    Foo(int value)
    {
        Init();
        // do something with value
    }
 
    void Init()
    {
        // code to init Foo
    }
};
```


> One small caveat: be careful when using Init() functions and dynamically allocated memory. Because Init() functions can be called by anyone at any time, dynamically allocated memory may or may not have already been allocated when Init() is called. Be careful to handle this situation appropriately -- it can be slightly confusing, since a non-null pointer could be either dynamically allocated memory or an uninitialized pointer!



## `Delegating constructors in C++11`


> Starting with C++11, constructors are now allowed to call other constructors. This process is called **delegating constructors** (or **constructor chaining**). To have one constructor call another, simply call the constructor in the member initializer list.  Make sure you’re calling the constructor from the member initializer list, not in the body of the constructor. 

```c
#include <string>
#include <iostream>
 
class Employee
{
private:
    int m_id;
    std::string m_name;
 
public:
    Employee(int id=0, const std::string &name=""):
        m_id(id), m_name(name)
    {
        std::cout << "Employee " << m_name << " created.\n";
    }
 
    // Use a delegating constructors to minimize redundant code
    Employee(const std::string &name) : Employee(0, name) { }
};
```


> A few additional notes about delegating constructors. First, a constructor that delegates to another constructor is not allowed to do any member initialization itself. So your constructors can delegate or initialize, but not both. Second, it’s possible for one constructor to delegate to another constructor, which delegates back to the first constructor. This forms an infinite loop, and will cause your program to run out of stack space and crash. You can avoid this by ensuring all of your constructors resolve to a non-delegating constructor.