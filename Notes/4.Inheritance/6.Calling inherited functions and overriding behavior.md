# Calling inherited functions and overriding behavior
---


## `Calling a base class function`

> When a member function is called with a derived class object, the compiler first looks to see if that member exists in the derived class. If not, it begins walking up the inheritance chain and checking whether the member has been defined in any of the parent classes. It uses the first one it finds.



## `Redefining behaviors`

> To modify the way a function defined in a base class works in the derived class, simply redefine the function in the derived class.
    
    
> Note that when you redefine a function in the derived class, the derived function does not inherit the access specifier of the function with the same name in the base class. It uses whatever access specifier it is defined under in the derived class. Therefore, a function that is defined as private in the base class can be redefined as public in the derived class, or vice-versa!

```c
class Base
{
private:
	void print()
	{
		std::cout << "Base";
	}
};
 
class Derived : public Base
{
public:
	void print()
	{
		std::cout << "Derived ";
	}
 
};
 
 
int main()
{
	Derived derived;
	derived.print(); // calls derived::print(), which is public
	return 0;
}
```




## `Adding to existing functionality`

> Sometimes we don’t want to completely replace a base class function, but instead want to add additional functionality to it. In the above example, note that Derived::identify() completely hides Base::identify()! This may not be what we want. It is possible to have our derived function call the base version of the function of the same name (in order to reuse code) and then add additional functionality to it.

```c
class Derived: public Base
{
public:
    Derived(int value)
        : Base(value)
    {
    }
 
    int GetValue() { return m_value; }
 
    void identify()
    {
        Base::identify(); // call Base::identify() first
        std::cout << "I am a Derived\n"; // then identify ourselves
    }
};
```



> There’s one bit of trickiness that we can run into when trying to call friend functions in base classes, such as operator<<. Because friend functions of the base class aren’t actually part of the base class, using the scope resolution qualifier won’t work. Instead, we need a way to make our Derived class temporarily look like the Base class so that the right version of the function can be called. Fortunately, that’s easy to do, using static_cast. 

```c
class Base
{
private:
	int m_value;
 
public:
	Base(int value)
		: m_value(value)
	{
	}
 
	friend std::ostream& operator<< (std::ostream &out, const Base &b)
	{
		out << "In Base\n";
		out << b.m_value << '\n';
		return out;
	}
};
 
class Derived : public Base
{
public:
	Derived(int value)
		: Base(value)
	{
	}
 
	friend std::ostream& operator<< (std::ostream &out, const Derived &d)
	{
		out << "In Derived\n";
		// static_cast Derived to a Base object, so we call the right version of operator<<
		out << static_cast<Base>(d); 
		return out;
	}
};
 
int main()
{
	Derived derived(7);
 
	std::cout << derived;
 
	return 0;
}
```


