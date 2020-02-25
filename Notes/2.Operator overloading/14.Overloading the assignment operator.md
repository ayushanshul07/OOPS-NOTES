#             Overloading the assignment operator
---

> The **assignment operator** (operator=) is used to copy values from one object to another *already existing object*.



## `Assignment vs Copy constructor`

> If a new object has to be created before the copying can occur, the copy constructor is used (note: this includes passing or returning objects by value).

> If a new object does not have to be created before the copying can occur, the assignment operator is used.



## `Overloading the assignment operator`

> The assignment operator must be overloaded as a member function.

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
	Fraction(const Fraction &copy) :
		m_numerator(copy.m_numerator), m_denominator(copy.m_denominator)
	{
		// no need to check for a denominator of 0 here since copy must already be a valid Fraction
		std::cout << "Copy constructor called\n"; // just to prove it works
	}
 
        // Overloaded assignment
        Fraction& operator= (const Fraction &fraction);
 
	friend std::ostream& operator<<(std::ostream& out, const Fraction &f1);
        
};
 
std::ostream& operator<<(std::ostream& out, const Fraction &f1)
{
	out << f1.m_numerator << "/" << f1.m_denominator;
	return out;
}
 
// A simplistic implementation of operator= (see better implementation below)
Fraction& Fraction::operator= (const Fraction &fraction)
{
    // do the copy
    m_numerator = fraction.m_numerator;
    m_denominator = fraction.m_denominator;
 
    // return the existing object so we can chain this operator
    return *this;
}
 
int main()
{
    Fraction fiveThirds(5, 3);
    Fraction f;
    f = fiveThirds; // calls overloaded assignment
    std::cout << f;
 
    return 0;
}
```




## `Issues due to self-assignment`

> In most cases, a self-assignment doesn’t need to do anything at all. However, in cases where an assignment operator needs to dynamically assign memory, self-assignment can actually be dangerous:

```c
#include <iostream>
 
class MyString
{
private:
    char *m_data;
    int m_length;
 
public:
    MyString(const char *data="", int length=0) :
        m_length(length)
    {
        if (!length)
            m_data = nullptr;
        else 
            m_data = new char[length];
 
        for (int i=0; i < length; ++i)
            m_data[i] = data[i];
    }
 
    // Overloaded assignment
    MyString& operator= (const MyString &str);
 
    friend std::ostream& operator<<(std::ostream& out, const MyString &s);
};
 
std::ostream& operator<<(std::ostream& out, const MyString &s)
{
    out << s.m_data;
    return out;
}
 
// A simplistic implementation of operator= (do not use)
MyString& MyString::operator= (const MyString &str)
{
    // if data exists in the current string, delete it
    if (m_data) delete[] m_data;
 
    m_length = str.m_length;
 
    // copy the data from str to the implicit object
    m_data = new char[str.m_length];
 
    for (int i=0; i < str.m_length; ++i)
        m_data[i] = str.m_data[i];
 
    // return the existing object so we can chain this operator
    return *this;
}
 
int main()
{
    MyString alex("Alex", 5); // Meet Alex
    alex = alex; // Alex is our newest employee
    std::cout << employee; // We will probably get garbage output
 
    return 0;
}
```



## `Detecting and handling self-assignment`

```c
// A simplistic implementation of operator= (do not use)
MyString& MyString::operator= (const MyString& str)
{
	// self-assignment check
	if (this == &str)
		return *this;
 
	// if data exists in the current string, delete it
	if (m_data) delete[] m_data;
 
	m_length = str.m_length;
 
	// copy the data from str to the implicit object
	m_data = new char[str.m_length];
 
	for (int i = 0; i < str.m_length; ++i)
		m_data[i] = str.m_data[i];
 
	// return the existing object so we can chain this operator
	return *this;
}
```



## `When not to handle self-assignment`

> First, there is no need to check for self-assignment in a copy-constructor. This is because the copy constructor is only called when new objects are being constructed, and there is no way to assign a newly created object to itself in a way that calls to copy constructor.

> Second, the self-assignment check may be omitted in classes that can naturally handle self-assignment.



## `Default assignment operator`

> Unlike other operators, the compiler will provide a default public assignment operator for your class if you do not provide one. This assignment operator does memberwise assignment (which is essentially the same as the memberwise initialization that default copy constructors do).


> Just like other constructors and operators, you can prevent assignments from being made by making your assignment operator private or using the delete keyword:

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
	Fraction(const Fraction &copy) = delete;
 
	// Overloaded assignment
	Fraction& operator= (const Fraction &fraction) = delete; // no copies through assignment!
 
	friend std::ostream& operator<<(std::ostream& out, const Fraction &f1);
        
};
 
std::ostream& operator<<(std::ostream& out, const Fraction &f1)
{
	out << f1.m_numerator << "/" << f1.m_denominator;
	return out;
}
 
int main()
{
    Fraction fiveThirds(5, 3);
    Fraction f;
    f = fiveThirds; // compile error, operator= has been deleted
    std::cout << f;
 
    return 0;
}
```





