# Overloading the increment and decrement operators
---


> Because the increment and decrement operators are both unary operators and they modify their operands, they’re best overloaded as member functions.



## `Overloading prefix increment and decrement`

```c
#include <iostream>
 
class Digit
{
private:
    int m_digit;
public:
    Digit(int digit=0)
        : m_digit(digit)
    {
    }
 
    Digit& operator++();
    Digit& operator--();
 
    friend std::ostream& operator<< (std::ostream &out, const Digit &d);
};
 
Digit& Digit::operator++()
{
    // If our number is already at 9, wrap around to 0
    if (m_digit == 9)
        m_digit = 0;
    // otherwise just increment to next number
    else
        ++m_digit;
 
    return *this;
}
 
Digit& Digit::operator--()
{
    // If our number is already at 0, wrap around to 9
    if (m_digit == 0)
        m_digit = 9;
    // otherwise just decrement to next number
    else
        --m_digit;
 
    return *this;
}
 
std::ostream& operator<< (std::ostream &out, const Digit &d)
{
	out << d.m_digit;
	return out;
}
 
int main()
{
    Digit digit(8);
 
    std::cout << digit;
    std::cout << ++digit;
    std::cout << ++digit;
    std::cout << --digit;
    std::cout << --digit;
 
    return 0;
}
```



## `Overloading postfix increment and decrement`

> Normally, functions can be overloaded when they have the same name but a different number and/or different type of parameters. However, consider the case of the prefix and postfix increment and decrement operators. Both have the same name (eg. operator++), are unary, and take one parameter of the same type. Therefore, C++ uses a “dummy variable” or “dummy argument” for the postfix operators. This argument is a fake integer parameter that only serves to distinguish the postfix version of increment/decrement from the prefix version.

```c
class Digit
{
private:
    int m_digit;
public:
    Digit(int digit=0)
        : m_digit(digit)
    {
    }
 
    Digit& operator++(); // prefix
    Digit& operator--(); // prefix
 
    Digit operator++(int); // postfix
    Digit operator--(int); // postfix
 
    friend std::ostream& operator<< (std::ostream &out, const Digit &d);
};
 
Digit& Digit::operator++()
{
    // If our number is already at 9, wrap around to 0
    if (m_digit == 9)
        m_digit = 0;
    // otherwise just increment to next number
    else
        ++m_digit;
 
    return *this;
}
 
Digit& Digit::operator--()
{
    // If our number is already at 0, wrap around to 9
    if (m_digit == 0)
        m_digit = 9;
    // otherwise just decrement to next number
    else
        --m_digit;
 
    return *this;
}
 
Digit Digit::operator++(int)
{
    // Create a temporary variable with our current digit
    Digit temp(m_digit);
 
    // Use prefix operator to increment this digit
    ++(*this); // apply operator
 
    // return temporary result
    return temp; // return saved state
}
 
Digit Digit::operator--(int)
{
    // Create a temporary variable with our current digit
    Digit temp(m_digit);
 
    // Use prefix operator to decrement this digit
    --(*this); // apply operator
 
    // return temporary result
    return temp; // return saved state
}
 
std::ostream& operator<< (std::ostream &out, const Digit &d)
{
	out << d.m_digit;
	return out;
}
 
int main()
{
    Digit digit(5);
 
    std::cout << digit;
    std::cout << ++digit; // calls Digit::operator++();
    std::cout << digit++; // calls Digit::operator++(int);
    std::cout << digit;
    std::cout << --digit; // calls Digit::operator--();
    std::cout << digit--; // calls Digit::operator--(int);
    std::cout << digit;
 
    return 0;
}
```


> Because the dummy parameter is not used in the function implementation, we have not even given it a name. This tells the compiler to treat this variable as a placeholder, which means it won’t warn us that we declared a variable but never used it.


>  In postfix operators, the caller receives a copy of the object before it was incremented or decremented, but the object itself is incremented or decremented. Note that this means the return value of the overloaded operator must be a non-reference, because we can’t return a reference to a local variable that will be destroyed when the function exits. Also note that this means the postfix operators are typically less efficient than the prefix operators because of the added overhead of instantiating a temporary variable and returning by value instead of reference.