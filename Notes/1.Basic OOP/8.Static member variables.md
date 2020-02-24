#                    Static member variables
---


> **Static members are not associated with class objects.** 

```c
class Something
{
private:
    static int s_value; // declares the static member variable
};
 
int Something::s_value = 1; // initializer, this is okay even though s_value is private since it's a definitionsection below)
 
int main()
{
    // note: we're not instantiating any objects of type Something
 
    Something::s_value = 2;
    std::cout << Something::s_value << '\n';
    return 0;
}
```


> When we declare a static member variable inside a class, we’re telling the compiler about the existence of a static member variable, but not actually defining it (much like a forward declaration). Because static member variables are not part of the individual class objects (they are treated similarly to global variables, and get initialized when the program starts), you must explicitly **define the static member outside of the class, in the global scope**.


> **Note:** This static member definition is not subject to access controls: you can define and initialize the value even if it’s declared as private (or protected) in the class.


> Do not put the static member definition in a header file (much like a global variable, if that header file gets included more than once, you’ll end up with multiple definitions, which will cause a compile error).


> When the static member is a const integral type (which includes char and bool) or a const enum, the static member can be initialized inside the class definition:

```c
class Whatever
{
public:
    static const int s_value = 4; // a static const int can be declared and initialized directly
};
```


>  As of C++11, static constexpr members of any type that supports constexpr initialization can be initialized inside the class definition:

```c
#include <array>
 
class Whatever
{
public:
    static constexpr double s_value = 2.2; // ok
    static constexpr std::array<int, 3> s_array = { 1, 2, 3 }; // this even works for classes that support constexpr initialization
};
```


