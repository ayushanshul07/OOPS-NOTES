# Constructors and initialization of derived classes
---

> With non-derived classes, constructors only have to worry about their own members.Here’s what actually happens when base is instantiated:
>     **1**. Memory for base is set aside
>     **2**. The appropriate Base constructor is called
>     **3**. The initialization list initializes variables
>     **4**. The body of the constructor executes
>     **5**. Control is returned to the caller



> Here’s what actually happens when derived is instantiated:
>     **1**. Memory for derived is set aside (enough for both the Base and Derived portions)
>     **2**. The appropriate Derived constructor is called
>     **3**. The Base object is constructed first using the appropriate Base constructor. If no base constructor is specified, the default constructor will be used.
>     **4**. The initialization list initializes variables
>     **5**. The body of the constructor executes
>     **6**. Control is returned to the caller




## `Initializing base class members`

```c
class Base
{
public:
    int m_id;
 
    Base(int id=0)
        : m_id(id)
    {
    }
 
    int getId() const { return m_id; }
};
```

> New programmers often attempt to solve this problem as follows:

```c
class Derived: public Base
{
public:
    double m_cost;
 
    Derived(double cost=0.0, int id=0)
        // does not work
        : m_cost(cost), m_id(id)
    {
    }
 
    double getCost() const { return m_cost; }
};
```

> C++ prevents classes from initializing inherited member variables in the initialization list of a constructor. In other words, the value of a variable can only be set in an initialization list of a constructor belonging to the same class as the variable.

> Consider what would happen if m_id were const. Because const variables must be initialized with a value at the time of creation, the base class constructor must set its value when the variable is created. However, when the base class constructor finishes, the derived class constructors initialization lists are then executed. Each derived class would then have the opportunity to initialize that variable, potentially changing its value! By restricting the initialization of variables to the constructor of the class those variables belong to, C++ ensures that all variables are initialized only once.



> New programmers often also try this:

```c
class Derived: public Base
{
public:
    double m_cost;
 
    Derived(double cost=0.0, int id=0)
        : m_cost(cost)
    {
        m_id = id;
    }
 
    double getCost() const { return m_cost; }
};
```

> While this actually works in this case, it wouldn’t work if m_id were a const or a reference (because const values and references have to be initialized in the initialization list of the constructor). It’s also inefficient because m_id gets assigned a value twice: once in the initialization list of the Base class constructor, and then again in the body of the Derived class constructor. And finally, what if the Base class needed access to this value during construction? It has no way to access it, since it’s not set until the Derived constructor is executed (which pretty much happens last).



> C++ gives us the ability to explicitly choose which Base class constructor will be called! To do this, simply add a call to the base class Constructor in the initialization list of the derived class:

```c
class Derived: public Base
{
public:
    double m_cost;
 
    Derived(double cost=0.0, int id=0)
        : Base(id), // Call Base(int) constructor with value id!
            m_cost(cost)
    {
    }
 
    double getCost() const { return m_cost; }
};

```


> Here’s what happens:
>     **1**. Memory for derived is allocated.
>     **2**. The Derived(double, int) constructor is called, where cost = 1.3, and id = 5
>     **3**. The compiler looks to see if we’ve asked for a particular Base class constructor. We have! So it calls Base(int) with id = 5.
>     **4.** The base class constructor initialization list sets m_id to 5
>     **5**. The base class constructor body executes, which does nothing
>     **6**. The base class constructor returns
>     **7**. The derived class constructor initialization list sets m_cost to 1.3
>     **8**. The derived class constructor body executes, which does nothing
>     **9**. The derived class constructor returns.


> Note that it doesn’t matter where in the Derived constructor initialization list the Base constructor is called -- it will always execute first.


> It is worth mentioning that constructors can only call constructors from their immediate parent/base class.



## `Destructors`

> When a derived class is destroyed, each destructor is called in the **reverse order of construction**.



