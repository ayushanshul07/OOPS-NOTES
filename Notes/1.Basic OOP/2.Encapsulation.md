#                               `Encapsulation`
---


> In object-oriented programming, **Encapsulation** (also called **information hiding**) is the process of keeping the details about how an object is implemented hidden away from users of the object. Instead, users of the object access the object through a public interface.

> **In C++, we implement encapsulation via access specifiers.** Typically, all member variables of the class are made private (hiding the implementation details), and most member functions are made public (exposing an interface for the user).

> **Benefit**: encapsulated classes are easier to use and reduce the complexity of your programs.

> **Benefit**: encapsulated classes help protect your data and prevent misuse.

> **Benefit**: encapsulated classes are easier to change.

> **Benefit**: encapsulated classes are easier to debug.

> An **access function** is a short public function whose job is to retrieve or change the value of a private member variable. Access functions typically come in two flavors: **getters** and **setters**. Getters (also sometimes called accessors) are functions that return the value of a private member variable. Setters (also sometimes called mutators) are functions that set the value of a private member variable.

> **Best practice**: Getters should return by value or const reference.



