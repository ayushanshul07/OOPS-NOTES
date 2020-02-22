#                                 Composition
---

## `Object composition`

> In real-life, complex objects are often built from smaller, simpler objects. This process of building complex objects from simpler ones is called **object composition**.


> In C++, you’ve already seen that structs and classes can have data members of various types (such as fundamental types or other classes). When we build classes with data members, we’re essentially constructing a complex object from simpler parts, which is object composition. For this reason, structs and classes are sometimes referred to as **composite types**.



## `Types of object composition`

> There are two basic subtypes of object composition: **composition** and **aggregation**.



## `Composition`

> To qualify as a composition, an object and a part must have the following relationship:
>     **1**. The part (member) is part of the object (class)
>     **2**. The part (member) can only belong to one object (class) at a time
>     **3**. The part (member) has its existence managed by the object (class)
>     **4**. The part (member) does not know about the existence of the object (class)



## `Implementing compositions`

> Compositions are one of the easiest relationship types to implement in C++. They are typically created as structs or classes with normal data members. Because these data members exist directly as part of the struct/class, their lifetimes are bound to that of the class instance itself.


> Compositions that need to do dynamic allocation or deallocation may be implemented using pointer data members. In this case, the composition class should be responsible for doing all necessary memory management itself (not the user of the class).


> In general, if you can design a class using composition, you should design a class using composition. Classes designed using composition are straightforward, flexible, and robust (in that they clean up after themselves nicely).


> The key point here is that the composition should manage its parts without the user of the composition needing to manage anything.
