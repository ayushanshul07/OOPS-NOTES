#             Early binding and late binding
---

> **Binding** refers to the process that is used to convert identifiers (such as variable and function names) into addresses. 



## `Early binding`

> A direct function call is a statement that directly calls a function. Direct function calls can be resolved using a process known as early binding. Early binding (also called static binding) means the compiler (or linker) is able to directly associate the identifier name (such as a function or variable name) with a machine address. 

```c
#include <iostream>
 
int add(int x, int y)
{
    return x + y;
}
 
int subtract(int x, int y)
{
    return x - y;
}
 
int multiply(int x, int y)
{
    return x * y;
}
 
int main()
{
    int x;
    std::cout << "Enter a number: ";
    std::cin >> x;
 
    int y;
    std::cout << "Enter another number: ";
    std::cin >> y;
 
    int op;
    do
    {
        std::cout << "Enter an operation (0=add, 1=subtract, 2=multiply): ";
        std::cin >> op;
    } while (op < 0 || op > 2);
 
    int result = 0;
    switch (op)
    {
        // call the target function directly using early binding
        case 0: result = add(x, y); break;
        case 1: result = subtract(x, y); break;
        case 2: result = multiply(x, y); break;
    }
 
    std::cout << "The answer is: " << result << std::endl;
 
    return 0;
}
```


## `Late Binding`

> In some programs, it is not possible to know which function will be called until runtime (when the program is run). This is known as late binding (or dynamic binding). In C++, one way to get late binding is to use function pointers. Calling a function via a function pointer is also known as an indirect function call. 

```c
#include <iostream>
 
int add(int x, int y)
{
    return x + y;
}
 
int subtract(int x, int y)
{
    return x - y;
}
 
int multiply(int x, int y)
{
    return x * y;
}
 
int main()
{
    int x;
    std::cout << "Enter a number: ";
    std::cin >> x;
 
    int y;
    std::cout << "Enter another number: ";
    std::cin >> y;
 
    int op;
    do
    {
        std::cout << "Enter an operation (0=add, 1=subtract, 2=multiply): ";
        std::cin >> op;
    } while (op < 0 || op > 2);
 
    // Create a function pointer named pFcn (yes, the syntax is ugly)
    int (*pFcn)(int, int) = nullptr;
 
    // Set pFcn to point to the function the user chose
    switch (op)
    {
        case 0: pFcn = add; break;
        case 1: pFcn = subtract; break;
        case 2: pFcn = multiply; break;
    }
 
    // Call the function that pFcn is pointing to with x and y as parameters
    // This uses late binding
    std::cout << "The answer is: " << pFcn(x, y) << std::endl;
 
    return 0;
}
```

> The compiler is unable to use early binding to resolve the function call pFcn(x, y) because it can not tell which function pFcn will be pointing to at compile time!


> Late binding is slightly less efficient since it involves an extra level of indirection. With early binding, the CPU can jump directly to the function’s address. With late binding, the program has to read the address held in the pointer and then jump to that address. This involves one extra step, making it slightly slower. However, the advantage of late binding is that it is more flexible than early binding, because decisions about what function to call do not need to be made until run time.
