#             Overloading the subscript operator
---


## `Overloading operator[]`

> The subscript operator is one of the operators that must be overloaded as a member function. An overloaded operator[] function will always take one parameter: the subscript that the user places between the hard braces.

```c
class IntList
{
private:
    int m_list[10];
 
public:
    int& operator[] (int index);
};
 
int& IntList::operator[] (int index)
{
    return m_list[index];
}
```


## `Dealing with const objects`

```c
class IntList
{
private:
    int m_list[10] = { 0, 1, 2, 3, 4, 5, 6, 7, 8, 9 }; // give this class some initial state for this example
 
public:
    int& operator[] (int index);
    const int& operator[] (int index) const;
};
 
int& IntList::operator[] (int index) // for non-const objects: can be used for assignment
{
    return m_list[index];
}
 
const int& IntList::operator[] (int index) const // for const objects: can only be used for access
{
    return m_list[index];
}
 
int main()
{
    IntList list;
    list[2] = 3; // okay: calls non-const version of operator[]
    std::cout << list[2];
 
    const IntList clist;
    clist[2] = 3; // compile error: calls const version of operator[], which returns a const reference.  Cannot assign to this.
    std::cout << clist[2];
 
    return 0;
}
```



## `Pointers to objects and overloaded operator[] don’t mix`

```c
#include <cassert> // for assert()
 
class IntList
{
private:
    int m_list[10];
 
public:
    int& operator[] (int index);
};
 
int& IntList::operator[] (int index)
{
    assert(index >= 0 && index < 10);
 
    return m_list[index];
}
 
int main()
{
    IntList *list = new IntList;
    list [2] = 3; // error: this will assume we're accessing index 2 of an array of IntLists
    delete list;
 
    return 0;
}
```

> Because we can’t assign an integer to an IntList, this won’t compile. However, if assigning an integer was valid, this would compile and run, with undefined results.


> The proper syntax would be to dereference the pointer first (making sure to use parenthesis since operator[] has higher precedence than operator*), then call operator[]:

```c
int main()
{
    IntList *list = new IntList;
    (*list)[2] = 3; // get our IntList object, then call overloaded operator[]
    delete list;
 
    return 0;
}
```



## `The function parameter does not need to be an integer`

```c
#include <iostream>
#include <string>
 
class Stupid
{
private:
 
public:
	void operator[] (std::string index);
};
 
// It doesn't make sense to overload operator[] to print something
// but it is the easiest way to show that the function parameter can be a non-integer
void Stupid::operator[] (std::string index)
{
	std::cout << index;
}
 
int main()
{
	Stupid stupid;
	stupid["Hello, world!"];
 
	return 0;
}
```

