#         Overloading the parenthesis operator
---


> The parenthesis operator (operator()) is a particularly interesting operator in that it allows you to vary both the type AND number of parameters it takes. 


> There are two things to keep in mind: first, the parenthesis operator must be implemented as a member function. Second, in non-object-oriented C++, the () operator is used to call functions. In the case of classes, operator() is just a normal operator that calls a function (named operator()) like any other overloaded operator.


```c
#include <cassert> // for assert()
class Matrix
{
private:
    double data[4][4];
public:
    Matrix()
    {
        // Set all elements of the matrix to 0.0
        for (int row=0; row < 4; ++row)
            for (int col=0; col < 4; ++col)
                data[row][col] = 0.0;
    }
 
    double& operator()(int row, int col);
    const double& operator()(int row, int col) const; // for const objects
};
 
double& Matrix::operator()(int row, int col)
{
    assert(col >= 0 && col < 4);
    assert(row >= 0 && row < 4);
 
    return data[row][col];
}
 
const double& Matrix::operator()(int row, int col) const
{
    assert(col >= 0 && col < 4);
    assert(row >= 0 && row < 4);
 
    return data[row][col];
}
```



## `Having fun with functors`

Operator() is also commonly overloaded to implement **functors** (or **function object**), which are classes that operate like functions. The advantage of a functor over a normal function is that functors can store data in member variables (since they are classes).

```c
class Accumulator
{
private:
    int m_counter = 0;
 
public:
    Accumulator()
    {
    }
 
    int operator() (int i) { return (m_counter += i); }
};
 
int main()
{
    Accumulator acc;
    std::cout << acc(10) << std::endl; // prints 10
    std::cout << acc(20) << std::endl; // prints 30
 
    return 0;
}
```


> You may wonder why we couldn’t do the same thing with a normal function and a static local variable to preserve data between function calls. We could, but because functions only have one global instance, we’d be limited to using it for one thing at a time. With functors, we can instantiate as many separate functor objects as we need and use them all simultaneously.
