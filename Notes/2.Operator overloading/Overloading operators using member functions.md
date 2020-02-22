# Overloading operators using member functions
---


> When overloading an operator using a member function:
>     **1.** The overloaded operator must be added as a member function of the left operand.
>     **2.** The left operand becomes the implicit *this object
>     **3.**  All other operands become function parameters.

```c
#include <iostream>
 
class Cents
{
private:
    int m_cents;
 
public:
    Cents(int cents) { m_cents = cents; }
 
    // Overload Cents + int
    Cents operator+(int value);
 
    int getCents() { return m_cents; }
};
 
// note: this function is a member function!
// the cents parameter in the friend version is now the implicit *this parameter
Cents Cents::operator+(int value)
{
    return Cents(m_cents + value);
}
 
int main()
{
	Cents cents1(6);
	Cents cents2 = cents1 + 2;
	std::cout << "I have " << cents2.getCents() << " cents.\n";
 
	return 0;
}
```

 
> **Not everything can be overloaded as a friend function.** The assignment (=), subscript ([]), function call (()), and member selection (->) operators must be overloaded as member functions, because the language requires them to be.


> **Not everything can be overloaded as a member function.** Typically, we won’t be able to use a member overload if the left operand is either not a class (e.g. int), or it is a class that we can’t modify (e.g. std::ostream).


> When dealing with binary operators that don’t modify the left operand (e.g. operator+), the normal or friend function version is typically preferred, because it works for all parameter types (even when the left operand isn’t a class object, or is a class that is not modifiable). The normal or friend function version has the added benefit of “symmetry”, as all operands become explicit parameters (instead of the left operand becoming *this and the right operand becoming an explicit parameter).


> When dealing with binary operators that do modify the left operand (e.g. operator+=), the member function version is typically preferred. In these cases, the leftmost operand will always be a class type, and having the object being modified become the one pointed to by *this is natural. Because the rightmost operand becomes an explicit parameter, there’s no confusion over who is getting modified and who is getting evaluated.


> Unary operators are usually overloaded as member functions as well, since the member version has no parameters.


> The following rules of thumb can help you determine which form is best for a given situation:
> **1.** If you’re overloading assignment (=), subscript ([]), function call (()), or member selection (->), do   so as a member function.
> **2.** If you’re overloading a unary operator, do so as a member function.
> **3.** If you’re overloading a binary operator that does not modify its left operand (e.g. operator+), do so as a normal function (preferred) or friend function.
> **4.** If you’re overloading a binary operator that modifies its left operand, but you can’t modify the definition of the left operand (e.g. operator<<, which has a left operand of type ostream), do so as a normal function (preferred) or friend function.
> **5.** If you’re overloading a binary operator that modifies its left operand (e.g. operator+=), and you can modify the definition of the left operand, do so as a member function.
