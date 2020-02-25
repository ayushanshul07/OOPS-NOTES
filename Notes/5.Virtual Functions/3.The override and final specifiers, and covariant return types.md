# The override and final specifiers, and covariant return types
---

> To address some common challenges with inheritance, C++11 added two special identifiers to C++: override and final. Note that these identifiers are not considered keywords -- they are normal identifiers that have special meaning in certain contexts.



## `The override specifier`

```c
class A
{
public:
	virtual const char* getName1(int x) { return "A"; }
	virtual const char* getName2(int x) { return "A"; }
};
 
class B : public A
{
public:
	virtual const char* getName1(short int x) { return "B"; } // note: parameter is a short int
	virtual const char* getName2(int x) const { return "B"; } // note: function is const
};
 
int main()
{
	B b;
	A &rBase = b;
	std::cout << rBase.getName1(1) << '\n';
	std::cout << rBase.getName2(2) << '\n';
    // This program prints:
    // A
    // A
	return 0;
}
```

> Because rBase is an A reference to a B object, the intention here is to use virtual functions to access B::getName1() and B::getName2(). However, because B::getName1() takes a different parameter (a short int instead of an int), it’s not considered an override of A::getName1(). More insidiously, because B::getName2() is const and A::getName2() isn’t, B::getName2() isn’t considered an override of A::getName2().


> To help address the issue of functions that are meant to be overrides but aren’t, C++11 introduced the override specifier. Override can be applied to any override function by placing the specifier in the same place const would go. If the function does not override a base class function, the compiler will flag the function as an error.

```c
class A
{
public:
	virtual const char* getName1(int x) { return "A"; }
	virtual const char* getName2(int x) { return "A"; }
	virtual const char* getName3(int x) { return "A"; }
};
 
class B : public A
{
public:
	virtual const char* getName1(short int x) override { return "B"; } // compile error, function is not an override
	virtual const char* getName2(int x) const override { return "B"; } // compile error, function is not an override
	virtual const char* getName3(int x) override { return "B"; } // okay, function is an override of A::getName3(int)
 
};
 
int main()
{
	return 0;
}
```

> **Rule**: Apply the override specifier to every intended override function you write.




## `The final specifier`

> There may be cases where you don’t want someone to be able to override a virtual function, or inherit from a class. The final specifier can be used to tell the compiler to enforce this. If the user tries to override a function or class that has been specified as final, the compiler will give a compile error.

```c
class A
{
public:
	virtual const char* getName() { return "A"; }
};
 
class B : public A
{
public:
	// note use of final specifier on following line -- that makes this function no longer overridable
	virtual const char* getName() override final { return "B"; } // okay, overrides A::getName()
};
 
class C : public B
{
public:
	virtual const char* getName() override { return "C"; } 
	// compile error: overrides B::getName(), which is final
};
```

> In the above code, B::getName() overrides A::getName(), which is fine. But B::getName() has the final specifier, which means that any further overrides of that function should be considered an error. And indeed, C::getName() tries to override B::getName() (the override specifier here isn’t relevant, it’s just there for good practice), so the compiler will give a compile error.


> In the case where we want to prevent inheriting from a class, the final specifier is applied after the class name:

```c
class A
{
public:
	virtual const char* getName() { return "A"; }
};
 
class B final : public A // note use of final specifier here
{
public:
	virtual const char* getName() override { return "B"; }
};
 
class C : public B // compile error: cannot inherit from final class
{
public:
	virtual const char* getName() override { return "C"; }
};
```




## `Covariant return types`

> There is one special case in which a derived class virtual function override can have a different return type than the base class and still be considered a matching override. If the return type of a virtual function is a pointer or a reference to a class, override functions can return a pointer or a reference to a derived class. These are called **covariant return types**.

```c
#include <iostream>
 
class Base
{
public:
	// This version of getThis() returns a pointer to a Base class
	virtual Base* getThis() { std::cout << "called Base::getThis()\n"; return this; }
	void printType() { std::cout << "returned a Base\n"; }
};
 
class Derived : public Base
{
public:
	// Normally override functions have to return objects of the same type as the base function
	// However, because Derived is derived from Base, it's okay to return Derived* instead of Base*
	virtual Derived* getThis() { std::cout << "called Derived::getThis()\n";  return this; }
	void printType() { std::cout << "returned a Derived\n"; }
};
 
int main()
{
	Derived d;
	Base *b = &d;
	d.getThis()->printType(); // calls Derived::getThis(), returns a Derived*, calls Derived::printType
	b->getThis()->printType(); // calls Derived::getThis(), returns a Base*, calls Base::printType
	
	// This prints:
    // called Derived::getThis()
    // returned a Derived
    // called Derived::getThis()
    // returned a Base
}
```

> In the above example, we first call d.getThis(). Since d is a Derived, this calls Derived::getThis(), which returns a Derived*. This Derived* is then used to call non-virtual function Derived::printType(). Now the interesting case. We then call b->getThis(). Variable b is a Base pointer to a Derived object. Base::getThis() is virtual function, so this calls Derived::getThis(). Although Derived::getThis() returns a Derived*, because base version of the function returns a Base*, the returned Derived* is upcast to a Base*. And thus, Base::printType() is called.

> In other words, in the above example, you only get a Derived* if you call getThis() with an object that is typed as a Derived object in the first place.