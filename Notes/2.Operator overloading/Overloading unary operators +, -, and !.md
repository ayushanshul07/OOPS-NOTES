#         Overloading unary operators +, -, and !
---


```c
#include <iostream>
 
class Point
{
private:
    double m_x, m_y, m_z;
 
public:
    Point(double x=0.0, double y=0.0, double z=0.0):
        m_x(x), m_y(y), m_z(z)
    {
    }
 
    // Convert a Point into its negative equivalent
    Point operator- () const;
 
    // Return true if the point is set at the origin
    bool operator! () const;
 
    double getX() { return m_x; }
    double getY() { return m_y; }
    double getZ() { return m_z; }
};
 
// Convert a Point into its negative equivalent 
Point Point::operator- () const
{
    return Point(-m_x, -m_y, -m_z);
}
 
// Return true if the point is set at the origin, false otherwise
bool Point::operator! () const
{
    return (m_x == 0.0 && m_y == 0.0 && m_z == 0.0);
}
 
int main()
{
    Point point; // use default constructor to set to (0.0, 0.0, 0.0)
 
    if (!point)
        std::cout << "point is set at the origin.\n";
    else
        std::cout << "point is not set at the origin.\n";
 
    return 0;
}
```


> Note that there’s no confusion between the negative operator- and the minus operator- since they have a different number of parameters.
