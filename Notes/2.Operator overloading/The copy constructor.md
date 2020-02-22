#                     The copy constructor
---

## `Recapping the types of initialization`

```c
#include <cassert>
#include <iostream>
 
class Fraction
{
private:
    int m_numerator;
    int m_denominator;
 
public:
    // Default constructor
    Fraction(int numerator=0, int denominator=1) :
        m_numerator(numerator), m_denominator(denominator)
    {
        assert(denominator != 0);
    }
 
    friend std::ostream& operator<<(std::ostream& out, const Fraction &f1);
};
 
std::ostream& operator<<(std::ostream& out, const Fraction &f1)
{
	out << f1.m_numerator << "/" << f1.m_denominator;
	return out;
}

int main(){
    int x(5); // Direct initialize an integer
    Fraction fiveThirds(5, 3); // Direct initialize a Fraction, calls Fraction(int, int) constructor
    
    // In C++11
    int x { 5 }; // Uniform initialization of an integer
    Fraction fiveThirds {5, 3}; // Uniform initialization of a Fraction, calls Fraction(int, int) constructor
    
    int x = 6; // Copy initialize an integer
    Fraction six = Fraction(6); // Copy initialize a Fraction, will call Fraction(6, 1)
    Fraction seven = 7; // Copy initialize a Fraction.  The compiler will try to find a way to convert 7 to a Fraction, which will invoke the Fraction(7, 1) constructor
    
    return 0;
}
```



## `The copy constructor`

> A **copy constructor** is a special type of constructor used to create a new object as a copy of an existing object. And much like a default constructor, if you do not provide a copy constructor for your classes, C++ will create a public copy constructor for you. Because the compiler does not know much about your class, by default, the created copy constructor utilizes a method of initialization called **memberwise initialization**. Memberwise initialization simply means that each member of the copy is initialized directly from the member of the class being copied. 

```c
#include <cassert>
#include <iostream>
 
class Fraction
{
private:
    int m_numerator;
    int m_denominator;
 
public:
    // Default constructor
    Fraction(int numerator=0, int denominator=1) :
        m_numerator(numerator), m_denominator(denominator)
    {
        assert(denominator != 0);
    }
 
    // Copy constructor
    Fraction(const Fraction &fraction) :
        m_numerator(fraction.m_numerator), m_denominator(fraction.m_denominator)
        // Note: We can access the members of parameter fraction directly, because we're inside the Fraction class
    {
        // no need to check for a denominator of 0 here since fraction must already be a valid Fraction
        std::cout << "Copy constructor called\n"; // just to prove it works
    }
 
    friend std::ostream& operator<<(std::ostream& out, const Fraction &f1);
};
 
std::ostream& operator<<(std::ostream& out, const Fraction &f1)
{
	out << f1.m_numerator << "/" << f1.m_denominator;
	return out;
}
 
int main()
{
	Fraction fiveThirds(5, 3); // Direct initialize a Fraction, calls Fraction(int, int) constructor
	Fraction fCopy(fiveThirds); // Direct initialize -- with Fraction copy constructor
	std::cout << fCopy << '\n';
}
```



## `Preventing copies`

> We can prevent copies of our classes from being made by making the copy constructor private:

```c
#include <cassert>
#include <iostream>
 
class Fraction
{
private:
    int m_numerator;
    int m_denominator;
 
    // Copy constructor (private)
    Fraction(const Fraction &fraction) :
        m_numerator(fraction.m_numerator), m_denominator(fraction.m_denominator)
    {
        // no need to check for a denominator of 0 here since fraction must already be a valid Fraction
        std::cout << "Copy constructor called\n"; // just to prove it works
    }
 
public:
    // Default constructor
    Fraction(int numerator=0, int denominator=1) :
        m_numerator(numerator), m_denominator(denominator)
    {
        assert(denominator != 0);
    }
 
    friend std::ostream& operator<<(std::ostream& out, const Fraction &f1);
};
 
std::ostream& operator<<(std::ostream& out, const Fraction &f1)
{
	out << f1.m_numerator << "/" << f1.m_denominator;
	return out;
}
 
int main()
{
	Fraction fiveThirds(5, 3); // Direct initialize a Fraction, calls Fraction(int, int) constructor
	Fraction fCopy(fiveThirds); // Copy constructor is private, compile error on this line
	std::cout << fCopy << '\n';
}
```



## `The copy constructor may be elided`

```c
#include <cassert>
#include <iostream>
 
class Fraction
{
private:
	int m_numerator;
	int m_denominator;
 
public:
    // Default constructor
    Fraction(int numerator=0, int denominator=1) :
        m_numerator(numerator), m_denominator(denominator)
    {
        assert(denominator != 0);
    }
 
        // Copy constructor
	Fraction(const Fraction &fraction) :
		m_numerator(fraction.m_numerator), m_denominator(fraction.m_denominator)
	{
		// no need to check for a denominator of 0 here since fraction must already be a valid Fraction
		std::cout << "Copy constructor called\n"; // just to prove it works
	}
 
	friend std::ostream& operator<<(std::ostream& out, const Fraction &f1);
};
 
std::ostream& operator<<(std::ostream& out, const Fraction &f1)
{
	out << f1.m_numerator << "/" << f1.m_denominator;
	return out;
}
 
int main()
{
	Fraction fiveThirds(Fraction(5, 3));
	std::cout << fiveThirds;
	
	//Expected output:
	//copy constructor called
    //5/3
    
    //Actual output:
    //5/3
    
	return 0;
}
```

> Note that initializing an anonymous object and then using that object to direct initialize our defined object takes two steps (one to create the anonymous object, one to call the copy constructor). However, the end result is essentially identical to just doing a direct initialization, which only takes one step. For this reason, in such cases, the compiler is allowed to opt out of calling the copy constructor and just do a direct initialization instead. This process is called **elision**.


> So although you wrote: *Fraction fiveThirds(Fraction(5, 3));* The compiler may change this to: *Fraction fiveThirds(5, 3);*


> Prior to C++17, copy elision is an optimization the compiler can make. As of C++17, some cases of copy elision (including the example above) have been made mandatory.


> Finally, **note** that if you make the copy constructor private, any initialization that would use the copy constructor will cause a compile error, even if the copy constructor is elided!
