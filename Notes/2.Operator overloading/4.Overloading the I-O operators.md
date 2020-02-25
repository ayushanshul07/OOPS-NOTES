#             Overloading the I/O operators
---


## `Overloading operator<<`

```c
#include <iostream>
 
class Point
{
private:
    double m_x, m_y, m_z;
 
public:
    Point(double x=0.0, double y=0.0, double z=0.0): m_x(x), m_y(y), m_z(z)
    {
    }
 
    // std::ostream is the type for object std::cout
    friend std::ostream& operator<< (std::ostream &out, const Point &point);
};
 
std::ostream& operator<< (std::ostream &out, const Point &point)
{
    // Since operator<< is a friend of the Point class, we can access Point's members directly.
    out << "Point(" << point.m_x << ", " << point.m_y << ", " << point.m_z << ")"; // actual output done here
 
    return out; // return std::ostream so we can chain calls to operator<<
}
 
int main()
{
    Point point1(2.0, 3.0, 4.0);
 
    std::cout << point1;
 
    return 0;
}
```


> The trickiest part here is the return type. With the arithmetic operators, we calculated and returned a single answer by value (because we were creating and returning a new result). However, if you try to return std::ostream by value, you’ll get a compiler error. This happens because std::ostream specifically disallows being copied.


> In this case, we return the left hand parameter as a reference. This not only prevents a copy of std::ostream from being made, it also allows us to “chain” output commands together, such as std::cout << point << std::endl;


> Any time we want our overloaded binary operators to be chainable in such a manner, the left operand should be returned (by reference). Returning the left-hand parameter by reference is okay in this case -- since the left-hand parameter was passed in by the calling function, it must still exist when the called function returns. Therefore, we don’t have to worry about referencing something that will go out of scope and get destroyed when the operator returns.



## `Overloading operator>>`

```c
#include <iostream>
 
class Point
{
private:
    double m_x, m_y, m_z;
 
public:
    Point(double x=0.0, double y=0.0, double z=0.0): m_x(x), m_y(y), m_z(z)
    {
    }
 
    friend std::ostream& operator<< (std::ostream &out, const Point &point);
    // std::istream is the type for object std::cin
    friend std::istream& operator>> (std::istream &in, Point &point);
};
 
std::ostream& operator<< (std::ostream &out, const Point &point)
{
    // Since operator<< is a friend of the Point class, we can access Point's members directly.
    out << "Point(" << point.m_x << ", " << point.m_y << ", " << point.m_z << ")";
 
    return out;
}
 
std::istream& operator>> (std::istream &in, Point &point)
{
    // Since operator>> is a friend of the Point class, we can access Point's members directly.
    // note that parameter point must be non-const so we can modify the class members with the input values
    in >> point.m_x;
    in >> point.m_y;
    in >> point.m_z;
 
    return in;
}
```




