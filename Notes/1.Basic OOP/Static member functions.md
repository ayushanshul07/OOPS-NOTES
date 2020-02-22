#                 Static member functions
---

> Static member functions are not attached to an object, they have no this pointer!


> Static member functions can directly access other static members (variables or functions), but not non-static members. This is because non-static members must belong to a class object, and static member functions have no class object to work with!


