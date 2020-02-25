# Overloading operators using normal functions
---


> If you don’t need direct access to the internal members of the classes you’re operating on., you can write your overloaded operators as normal functions.

```c
#include <iostream>
 
class Cents
{
private:
	int m_cents;
 
public:
	Cents(int cents) { m_cents = cents; }
 
	int getCents() const { return m_cents; }
};
 
// note: this function is not a member function nor a friend function!
Cents operator+(const Cents &c1, const Cents &c2)
{
	// use the Cents constructor and operator+(int, int)
        // we don't need direct access to private members here
	return Cents(c1.getCents() + c2.getCents());
}
 
int main()
{
	Cents cents1(6);
	Cents cents2(8);
	Cents centsSum = cents1 + cents2;
	std::cout << "I have " << centsSum.getCents() << " cents." << std::endl;
 
	return 0;
}
```


> **Rule**: Prefer overloading operators as normal functions instead of friends if it’s possible to do so without adding additional functions.
