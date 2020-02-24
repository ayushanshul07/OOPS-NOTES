# Pure virtual functions, abstract base classes, and interface classes
---

## `Pure virtual (abstract) functions and abstract base classes`

> So far, all of the virtual functions we have written have a body (a definition). However, C++ allows you to create a special kind of virtual function called a **pure virtual function** (or **abstract function**) that has no body at all! A pure virtual function simply acts as a placeholder that is meant to be redefined by derived classes.


> To create a pure virtual function, rather than define a body for the function, we simply assign the function the value 0.

```c
class Base
{
public:
    const char* sayHi() { return "Hi"; } // a normal non-virtual function    
 
    virtual const char* getName() { return "Base"; } // a normal virtual function
 
    virtual int getValue() = 0; // a pure virtual function
 
    int doSomething() = 0; // Compile error: can not set non-virtual functions to 0
};
```

> Using a pure virtual function has two main consequences: First, any class with one or more pure virtual functions becomes an **abstract base class**, which means that it can not be instantiated! Second, any derived class must define a body for this function, or that derived class will be considered an abstract base class as well.


> A pure virtual function is useful when we have a function that we want to put in the base class, but only the derived classes know what it should return. A pure virtual function makes it so the base class can not be instantiated, and the derived classes are forced to define these functions before they can be instantiated. This helps ensure the derived classes do not forget to redefine functions that the base class was expecting them to.




## `Pure virtual functions with bodies`

> It turns out that we can define pure virtual functions that have bodies:

```c
#include <string>
class Animal // This Animal is an abstract base class
{
protected:
    std::string m_name;
 
public:
    Animal(std::string name)
        : m_name(name)
    {
    }
 
    std::string getName() { return m_name; }
    virtual const char* speak() = 0; // The = 0 means this function is pure virtual
};
 
const char* Animal::speak()  // even though it has a body
{
    return "buzz";
}
```

> In this case, speak() is still considered a pure virtual function (even though it has been given a body, because of the “= 0”) and Animal is still considered an abstract base class (and thus can’t be instantiated). Any class that inherits from Animal needs to provide its own definition for speak() or it will also be considered an abstract base class.

> When providing a body for a pure virtual function, the body must be provided separately (not inline).

> This paradigm can be useful when you want your base class to provide a default implementation for a function, but still force any derived classes to provide their own implementation. However, if the derived class is happy with the default implementation provided by the base class, it can simply call the base class implementation directly.

```c
#include <string>
#include <iostream>
 
class Animal // This Animal is an abstract base class
{
protected:
    std::string m_name;
 
public:
    Animal(std::string name)
        : m_name(name)
    {
    }
 
    std::string getName() { return m_name; }
    virtual const char* speak() = 0; // note that speak is a pure virtual function
};
 
const char* Animal::speak()
{
    return "buzz"; // some default implementation
}
 
class Dragonfly: public Animal
{
 
public:
    Dragonfly(std::string name)
        : Animal(name)
    {
    }
 
    virtual const char* speak() // this class is no longer abstract because we defined this function
    {
        return Animal::speak(); // use Animal's default implementation
    }
};
 
int main()
{
    Dragonfly dfly("Sally");
    std::cout << dfly.getName() << " says " << dfly.speak() << '\n';
}
```

> This capability isn’t used very commonly.





## `Interface classes`

> An **interface class** is a class that has no member variables, and where all of the functions are pure virtual! In other words, the class is purely a definition, and has no actual implementation. Interfaces are useful when you want to define the functionality that derived classes must implement, but leave the details of how the derived class implements that functionality entirely up to the derived class.


> Interface classes are often named beginning with an I. Here’s a sample interface class:

```c
class IErrorLog
{
public:
    virtual bool openLog(const char *filename) = 0;
    virtual bool closeLog() = 0;
 
    virtual bool writeError(const char *errorMessage) = 0;
 
    virtual ~IErrorLog() {} // make a virtual destructor in case we delete an IErrorLog pointer, so the proper derived destructor is called
};
```

> Any class inheriting from IErrorLog must provide implementations for all three functions in order to be instantiated. You could derive a class named FileErrorLog, where openLog() opens a file on disk, closeLog() closes the file, and writeError() writes the message to the file. You could derive another class called ScreenErrorLog, where openLog() and closeLog() do nothing, and writeError() prints the message in a pop-up message box on the screen.


> Now, let’s say you need to write some code that uses an error log. If you write your code so it includes FileErrorLog or ScreenErrorLog directly, then you’re effectively stuck using that kind of error log (at least without recoding your program).

```c
#include <cmath> // for sqrt()
double mySqrt(double value, FileErrorLog &log)
{
    if (value < 0.0)
    {
        log.writeError("Tried to take square root of value less than 0");
        return 0.0;
    }
    else
        return sqrt(value);
}
```

> A much better way to implement this function is to use IErrorLog instead:

```c
#include <cmath> // for sqrt()
double mySqrt(double value, IErrorLog &log)
{
    if (value < 0.0)
    {
        log.writeError("Tried to take square root of value less than 0");
        return 0.0;
    }
    else
        return sqrt(value);
}
```

> Now the caller can pass in any class that conforms to the IErrorLog interface. If they want the error to go to a file, they can pass in an instance of FileErrorLog. If they want it to go to the screen, they can pass in an instance of ScreenErrorLog. Or if they want to do something you haven’t even thought of, such as sending an email to someone when there’s an error, they can derive a new class from IErrorLog (e.g. EmailErrorLog) and use an instance of that! By using IErrorLog, your function becomes more independent and flexible.


> Don’t forget to include a virtual destructor for your interface classes, so that the proper derived destructor will be called if a pointer to the interface is deleted.




## `Pure virtual functions and the virtual table`

> Abstract classes still have virtual tables, as these can still be used if you have a pointer or reference to the abstract class. The virtual table entry for a pure virtual function will generally either contain a null pointer, or point to a generic function that prints an error (sometimes this function is named __purecall) if no override is provided.