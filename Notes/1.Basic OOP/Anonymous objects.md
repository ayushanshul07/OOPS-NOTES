#                         Anonymous objects
---

> An **anonymous object** is essentially a value that has no name. Because they have no name, there’s no way to refer to them beyond the point where they are created. Consequently, they have “expression scope”, meaning they are created, evaluated, and destroyed all within a single expression.

```c
#include <iostream>
 
int add(int x, int y)
{
    return x + y; // an anonymous object is created to hold and return the result of x + y
}
 
int main()
{
    std::cout << add(5, 3);
 
    return 0;
}
```

> When the expression x + y is evaluated, the result is placed in an anonymous object. A copy of the anonymous object is then returned to the caller by value, and the anonymous object is destroyed.


