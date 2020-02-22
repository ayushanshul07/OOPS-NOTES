# Virtual destructors, virtual assignment, and overriding virtualization
---

## `Virtual destructors`

> Although C++ provides a default destructor for your classes if you do not provide one yourself, it is sometimes the case that you will want to provide your own destructor (particularly if the class needs to deallocate memory). You should always make your destructors virtual if you’re dealing with inheritance.

```c
#include <iostream>
class Base
{
public:
    ~Base() // note: not virtual
    {
        std::cout << "Calling ~Base()" << std::endl;
    }
};
 
class Derived: public Base
{
private:
    int* m_array;
 
public:
    Derived(int length)
    {
        m_array = new int[length];
    }
 
    ~Derived() // note: not virtual (your compiler may warn you about this)
    {
        std::cout << "Calling ~Derived()" << std::endl;
        delete[] m_array;
    }
};
 
int main()
{
    Derived *derived = new Derived(5);
    Base *base = derived ;
    delete base;
    // Output:
    // Calling ~Base() 
    return 0;
}
```

> Because base is a Base pointer, when base is deleted, the program looks to see if the Base destructor is virtual. It’s not, so it assumes it only needs to call the Base destructor.


> However, we really want the delete function to call Derived’s destructor (which will call Base’s destructor in turn), otherwise m_array will not be deleted. We do this by making Base’s destructor virtual:

```c
#include <iostream>
class Base
{
public:
    virtual ~Base() // note: virtual
    {
        std::cout << "Calling ~Base()" << std::endl;
    }
};
 
class Derived: public Base
{
private:
    int* m_array;
 
public:
    Derived(int length)
    {
        m_array = new int[length];
    }
 
    virtual ~Derived() // note: virtual
    {
        std::cout << "Calling ~Derived()" << std::endl;
        delete[] m_array;
    }
};
 
int main()
{
    Derived *derived = new Derived(5);
    Base *base = derived;
    delete base;
    // Output:
    // Calling ~Derived()
    // Calling ~Base()
    return 0;
}
```


> **Rule**: Whenever you are dealing with inheritance, you should make any explicit destructors virtual.




## `Ignoring virtualization`

> Very rarely you may want to ignore the virtualization of a function. For example, consider the following code:

```c
class Base
{
public:
    virtual const char* getName() { return "Base"; }
};
 
class Derived: public Base
{
public:
    virtual const char* getName() { return "Derived"; }
};
```

> There may be cases where you want a Base pointer to a Derived object to call Base::getName() instead of Derived::getName(). To do so, simply use the scope resolution operator:

```c
#include <iostream>
int main()
{
    Derived derived;
    Base &base = derived;
    // Calls Base::GetName() instead of the virtualized Derived::GetName()
    std::cout << base.Base::getName() << std::endl;
}
```




## `Should we make all destructors virtual?`

> Now that the final specifier has been introduced into the language, our recommendations are as follows:
>     **1**. If you intend your class to be inherited from, make sure your destructor is virtual.
>     **2**. If you do not intend your class to be inherited from, mark your class as final. This will prevent other classes from inheriting from it in the first place, without imposing any other use restrictions on the class itself.
