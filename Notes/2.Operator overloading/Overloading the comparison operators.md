#           Overloading the comparison operators
---


> Because the comparison operators are all binary operators that do not modify their left operands, we will make our overloaded comparison operators friend functions(or normal functions). Below are few examples:

```c
#include <iostream>
 
class Cents
{
private:
    int m_cents;
 
public:
    Cents(int cents) { m_cents = cents; }
 
    friend bool operator> (const Cents &c1, const Cents &c2);
    friend bool operator<= (const Cents &c1, const Cents &c2);
 
    friend bool operator< (const Cents &c1, const Cents &c2);
    friend bool operator>= (const Cents &c1, const Cents &c2);
};
 
bool operator> (const Cents &c1, const Cents &c2)
{
    return c1.m_cents > c2.m_cents;
}
 
bool operator>= (const Cents &c1, const Cents &c2)
{
    return c1.m_cents >= c2.m_cents;
}
 
bool operator< (const Cents &c1, const Cents &c2)
{
    return c1.m_cents < c2.m_cents;
}
 
bool operator<= (const Cents &c1, const Cents &c2)
{
    return c1.m_cents <= c2.m_cents;
}
 
int main()
{
    Cents dime(10);
    Cents nickel(5);
 
    if (nickel > dime)
        std::cout << "a nickel is greater than a dime.\n";
    if (nickel >= dime)
        std::cout << "a nickel is greater than or equal to a dime.\n";
    if (nickel < dime)
        std::cout << "a dime is greater than a nickel.\n";
    if (nickel <= dime)
        std::cout << "a dime is greater than or equal to a nickel.\n";
 
 
    return 0;
}

```
