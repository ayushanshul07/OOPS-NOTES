#                 Friend functions and classes
---


## `Friend functions`

> A **friend function** is a function that can access the private members of a class as though it were a member of that class. A friend function may be either a normal function, or a member function of another class. To declare a friend function, simply use the *friend* keyword in front of the prototype of the function you wish to be a friend of the class. It does not matter whether you declare the friend function in the private or public section of the class.

```c
class Accumulator 
{
private:
    int m_value;
public:
    Accumulator() { m_value = 0; } 
    void add(int value) { m_value += value; }
 
    // Make the reset() function a friend of this class
    friend void reset(Accumulator &accumulator);
};
 
// reset() is now a friend of the Accumulator class
void reset(Accumulator &accumulator)
{
    // And can access the private data of Accumulator objects
    accumulator.m_value = 0;
}
 
int main()
{
    Accumulator acc;
    acc.add(5); // add 5 to the accumulator
    reset(acc); // reset the accumulator to 0
 
    return 0;
}
```



## `Friend classes`

```c
#include <iostream>
 
class Storage
{
private:
    int m_nValue;
    double m_dValue;
public:
    Storage(int nValue, double dValue)
    {
        m_nValue = nValue;
        m_dValue = dValue;
    }
 
    // Make the Display class a friend of Storage
    friend class Display;
};
 
class Display
{
private:
    bool m_displayIntFirst;
 
public:
    Display(bool displayIntFirst) { m_displayIntFirst = displayIntFirst; }
 
    void displayItem(const Storage &storage)
    {
        if (m_displayIntFirst)
            std::cout << storage.m_nValue << " " << storage.m_dValue << '\n';
        else // display double first
            std::cout << storage.m_dValue << " " << storage.m_nValue << '\n';
    }
};
 
int main()
{
    Storage storage(5, 6.7);
    Display display(false);
 
    display.displayItem(storage);
 
    return 0;
}
```



> Be careful when using friend functions and classes, because it allows the friend function or class to violate encapsulation. If the details of the class change, the details of the friend will also be forced to change. Consequently, limit your use of friend functions and classes to a minimum.



## `Friend member functions`



## 
## 
## 
