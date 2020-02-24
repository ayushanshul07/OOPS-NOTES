#                 Hiding inherited functionality
---

## `Changing an inherited member’s access level`

> C++ gives us the ability to change an inherited member’s access specifier in the derived class. This is done by using a using declaration to identify the (scoped) base class member that is having its access changed in the derived class, under the new access specifier.

```c
#include <iostream>
 
class Base
{
private:
    int m_value;
 
public:
    Base(int value)
        : m_value(value)
    {
    }
 
protected:
    void printValue() { std::cout << m_value; }
};

class Derived: public Base
{
public:
    Derived(int value)
        : Base(value)
    {
    }
 
    // Base::printValue was inherited as protected, so the public has no access
    // But we're changing it to public via a using declaration
    using Base::printValue; // note: no parenthesis here
};

int main()
{
    Derived derived(7);
 
    // printValue is public in Derived, so this is okay
    derived.printValue(); // prints 7
    return 0;
}
```


> You can only change the access specifiers of base members the derived class would normally be able to access. Therefore, you can never change the access specifier of a base member from private to protected or public, because derived classes do not have access to private members of the base class.


> As of C++11, using-declarations are the preferred way of changing access levels. However, you can also change access levels by using an “access declaration”. This works identically to the using declaration method, but omits the “using” keyword. This access declaration way of redefining access is now considered deprecated.



## `Hiding functionality`

> In a derived class, it is possible to hide functionality that exists in the base class, so that it can not be accessed through the derived class. This can be done simply by changing the relevant access specifier.

```c
#include <iostream>
class Base
{
public:
	int m_value;
};
 
class Derived : public Base
{
private:
	using Base::m_value;
 
public:
	Derived(int value)
	// We can't initialize m_value, since it's a Base member (Base must initialize it)
	{
		// But we can assign it a value
		m_value = value;
	}
};
 
int main()
{
	Derived derived(7);
 
	// The following won't work because m_value has been redefined as private
	std::cout << derived.m_value;
 
	return 0;
}
```

> Note that this allowed us to take a poorly designed base class and encapsulate its data in our derived class. Alternatively, instead of inheriting Base’s members publicly and making m_value private by overriding its access specifier, we could have inherited Base privately, which would have caused all of Base’s member to be inherited privately in the first place.


> You can also mark member functions as deleted in the derived class, which ensures they can’t be called at all through a derived object:

```c
#include <iostream>
class Base
{
private:
	int m_value;
 
public:
	Base(int value)
		: m_value(value)
	{
	}
 
	int getValue() { return m_value; }
};
 
class Derived : public Base
{
public:
	Derived(int value)
		: Base(value)
	{
	}
 
 
	int getValue() = delete; // mark this function as inaccessible
};
 
int main()
{
	Derived derived(7);
 
	// The following won't work because getValue() has been deleted!
	std::cout << derived.getValue();
 
	return 0;
}
```



>  Note that the Base version of getValue() is still accessible though. This means that a Derived object can still access getValue() by upcasting the Derived object to a Base first:

```c
int main()
{
	Derived derived(7);
 
	// We can still access the function deleted in the Derived class through the Base class
	std::cout << static_cast<Base>(derived).getValue();
 
	return 0;
}
```
