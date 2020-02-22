#             Introduction to Operator overloading
---

> In C++, operators are implemented as functions. By using function overloading on the operator functions, you can define your own versions of the operators that work with different data types (including classes that you’ve written). Using function overloading to overload operators is called **operator overloading**.


## `Operators as functions`

```c
int x = 2;
int y = 3;
std::cout << x + y << '\n';
```

> The compiler comes with a built-in version of the plus operator (+) for integer operands -- this function adds integers x and y together and returns an integer result. When you see the expression x + y, you can translate this in your head to the function call operator+(x, y) (where operator+ is the name of the function).


## `Resolving overloaded operators`

> If all of the operands are fundamental data types, the compiler will call a built-in routine if one exists. If one does not exist, the compiler will produce a compiler error.

> If any of the operands are user data types (e.g. one of your classes, or an enum type), the compiler looks to see whether the type has a matching overloaded operator function that it can call. If it can’t find one, it will try to convert one or more of the user-defined type operands into fundamental data types so it can use a matching built-in operator (via an overloaded typecast). If that fails, then it will produce a compile error.


## `Limitations`

> Almost any existing operator in C++ can be overloaded. The exceptions are: conditional (?:), sizeof, scope (::), member selector (.), and member pointer selector (.*).

> You can only overload the operators that exist. You can not create new operators or rename existing operators. For example, you could not create an operator ** to do exponents.

> At least one of the operands in an overloaded operator must be a user-defined type. This means you can not overload the plus operator to work with one integer and one double. However, you could overload the plus operator to work with an integer and a Mystring.

> It is not possible to change the number of operands an operator supports.

> All operators keep their default precedence and associativity (regardless of what they’re used for) and this can not be changed.

> **Rule**: When overloading operators, it’s best to keep the function of the operators as close to the original intent of the operators as possible.

> **Rule**: If the meaning of an operator when applied to a custom class is not clear and intuitive, use a named function instead.





