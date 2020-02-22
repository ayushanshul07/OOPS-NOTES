#         Printing inherited classes using operator<<
---

## `The challenges with operator<<`

```c
#include <iostream>
class Base
{
public:
	Base() {}
 
	virtual void print() const { std::cout << "Base";  }
 
	friend std::ostream& operator<<(std::ostream &out, const Base &b)
        {
            out << "Base";
            return out;
        }
};
 
class Derived : public Base
{
public:
	Derived() {}
 
	virtual void print() const override { std::cout << "Derived"; }
 
	friend std::ostream& operator<<(std::ostream &out, const Derived &d)
        {
            out << "Derived";
            return out;
        }
 
};
 
int main()
{
    Base b;
    std::cout << b << '\n';
 
    Derived d;
    std::cout << d << '\n';
 
    Base &bref = d;
    std::cout << bref << '\n';
    
    // This prints:
    // Base
    // Derived
    // Base
    return 0;
}
```

> That’s probably not what we were expecting. This happens because our version of operator<< that handles Base objects isn’t virtual, so std::cout << bref calls the version of operator<< that handles Base objects rather than Derived objects.




## `Can we make Operator << virtual?`

> The short answer is no.


> First, only member functions can be virtualized -- this makes sense, since only classes can inherit from other classes, and there’s no way to override a function that lives outside of a class (you can overload non-member functions, but not override them). Because we typically implement operator<< as a friend, and friends aren’t considered member functions, a friend version of operator<< is ineligible to be virtualized.


> Second, even if we could virtualize operator<< there’s the problem that the function parameters for Base::operator<< and Derived::operator<< differ (the Base version would take a Base parameter and the Derived version would take a Derived parameter). Consequently, the Derived version wouldn’t be considered an override of the Base version, and thus be ineligible for virtual function resolution.





## `The solution`

```c
#include <iostream>
class Base
{
public:
	Base() {}
 
	// Here's our overloaded operator<<
	friend std::ostream& operator<<(std::ostream &out, const Base &b)
	{
		// Delegate printing responsibility for printing to member function print()
		return b.print(out);
	}
 
	// We'll rely on member function print() to do the actual printing
	// Because print is a normal member function, it can be virtualized
	virtual std::ostream& print(std::ostream& out) const
	{
		out << "Base";
		return out;
	}
};
 
class Derived : public Base
{
public:
	Derived() {}
 
	// Here's our override print function to handle the Derived case
	virtual std::ostream& print(std::ostream& out) const override
	{
		out << "Derived";
		return out;
	}
};
 
int main()
{
	Base b;
	std::cout << b << '\n';
 
	Derived d;
	std::cout << d << '\n'; // note that this works even with no operator<< that explicitly handles Derived objects
 
	Base &bref = d;
	std::cout << bref << '\n';

    // This prints
    // Base 
    // Derived
    // Derived
	return 0;
}
```

> First, in the Base case, we call operator<<, which calls virtual function print(). Since our Base reference parameter points to a Base object, b.print() resolves to Base::print(), which does the printing. Nothing too special here.

> In the Derived case, the compiler first looks to see if there’s an operator<< that takes a Derived object. There isn’t one, because we didn’t define one. Next the compiler looks to see if there’s an operator<< that takes a Base object. There is, so the compiler does an implicit upcast of our Derived object to a Base& and calls the function (we could have done this upcast ourselves, but the compiler is helpful in this regard). This function then calls virtual print(), which resolves to Derived::print().

> The third case proceeds as a mix of the first two. First, the compiler matches variable bref with operator<< that takes a Base. That calls our virtual print() function. Since the Base reference is actually pointing to a Derived object, this resolves to Derived::print(), as we intended.


