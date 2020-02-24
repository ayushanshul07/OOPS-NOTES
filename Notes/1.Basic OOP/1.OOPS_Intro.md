#                               OOPS Introduction
---

## `Introduction`

> Object-oriented programming (OOP) provides us with the ability to create objects that tie together both properties and behaviors into a self-contained, reusable package.

> This allows programs to be written in a more modular fashion, which makes them easier to write and understand, and also provides a higher degree of code-reusability. 


## `Classes`

> **Rule**: Name your classes starting with a capital letter.

> **Rule**: Use the struct keyword for data-only structures. Use the class keyword for objects that have both data and functions.

> **Access specifiers** determine who has access to the members that follow the specifier. Each of the members “acquires” the access level of the previous access specifier (or, if none is provided, the default access specifier).

> C++ provides 3 different access specifier keywords: **public**, **private**, and **protected**. 

> Public members are members of a struct or class that can be accessed from outside of the struct or class.

> Private members are members of a class that can only be accessed by other members of the class.

> All members of a struct are public members by default. By default, all members of a class are private.

> **Rule**: Make member variables private, and member functions public, unless you have a good reason not to.

> **Access control works on a per-class basis, not a per-object basis.** This means that when a function has access to the private members of a class, it can access the private members of any object of that class type that it can see.

```c
#include <iostream>
 
class DateClass // members are private by default
{
	int m_month; // private by default, can only be accessed by other members
	int m_day; // private by default, can only be accessed by other members
	int m_year; // private by default, can only be accessed by other members
 
public:
	void setDate(int month, int day, int year)
	{
		m_month = month;
		m_day = day;
		m_year = year;
	}
 
	void print()
	{
		std::cout << m_month << "/" << m_day << "/" << m_year;
	}
 
	// Note the addition of this function
	void copyFrom(const DateClass &d)
	{
		// Note that we can access the private members of d directly
		m_month = d.m_month;
		m_day = d.m_day;
		m_year = d.m_year;
	}
};

int main()
{
	DateClass date;
	date.setDate(10, 14, 2020); // okay, because setDate() is public
	
	DateClass copy;
	copy.copyFrom(date); // okay, because copyFrom() is public
	copy.print();
 
	return 0;
}
```
