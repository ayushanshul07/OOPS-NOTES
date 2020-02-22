#         Virtual functions and polymorphism
---

> A **virtual function** is a special type of function that, when called, resolves to the most-derived version of the function that exists between the base and derived class. This capability is known as **polymorphism**. A derived function is considered a match if it has the same signature (name, parameter types, and whether it is const) and return type as the base version of the function. Such functions are called **overrides**.

```c
class Base
{
public:
    virtual const char* getName() { return "Base"; } // note addition of virtual keyword
};
 
class Derived: public Base
{
public:
    virtual const char* getName() { return "Derived"; }
};
 
int main()
{
    Derived derived;
    Base &rBase = derived;
    std::cout << "rBase is a " << rBase.getName() << '\n';
     // This will print:
     // rBase is a Derived
    return 0;
}
```

> Because rBase is a reference to the Base portion of a Derived object, when rBase.getName() is evaluated, it would normally resolve to Base::getName(). However, Base::getName() is virtual, which tells the program to go look and see if there are any more-derived versions of the function available between Base and Derived. In this case, it will resolve to Derived::getName()!


```c
class A
{
public:
    virtual const char* getName() { return "A"; }
};
 
class B: public A
{
public:
    virtual const char* getName() { return "B"; }
};
 
class C: public B
{
public:
    virtual const char* getName() { return "C"; }
};
 
class D: public C
{
public:
    virtual const char* getName() { return "D"; }
};
 
int main()
{
    C c;
    A &rBase = c;
    std::cout << "rBase is a " << rBase.getName() << '\n';
    // This will print:
    // rBase is a C
    return 0;
}
```

> Let’s look at how this works. First, we instantiate a C class object. rBase is an A reference, which we set to reference the A portion of the C object. Finally, we call rBase.getName(). rBase.getName() evaluates to A::getName(). However, A::getName() is virtual, so the compiler will call the most-derived match between A and C. In this case, that is C::getName(). Note that it will not call D::getName(), because our original object was a C, not a D, so only functions between A and C are considered.


```c
#include <string>
class Animal
{
protected:
    std::string m_name;
 
    // We're making this constructor protected because
    // we don't want people creating Animal objects directly,
    // but we still want derived classes to be able to use it.
    Animal(std::string name)
        : m_name(name)
    {
    }
 
public:
    std::string getName() { return m_name; }
    virtual const char* speak() { return "???"; }
};
 
class Cat: public Animal
{
public:
    Cat(std::string name)
        : Animal(name)
    {
    }
 
    virtual const char* speak() { return "Meow"; }
};
 
class Dog: public Animal
{
public:
    Dog(std::string name)
        : Animal(name)
    {
    }
 
    virtual const char* speak() { return "Woof"; }
};
 
void report(Animal &animal)
{
    std::cout << animal.getName() << " says " << animal.speak() << '\n';
}
 
int main()
{
    Cat cat("Fred");
    Dog dog("Garbo");
 
    report(cat);
    report(dog);
    
  //  This program produces the result:
  //  Fred says Meow
  //  Garbo says Woof
}
```

> When animal.speak() is evaluated, the program notes that Animal::speak() is a virtual function. In the case where animal is referencing the Animal portion of a Cat object, the program looks at all the classes between Animal and Cat to see if it can find a more derived function. In that case, it finds Cat::speak(). In the case where animal references the Animal portion of a Dog object, the program resolves the function call to Dog::speak().


> Similarly, the following array example now works as expected:

```c
Cat fred("Fred"), misty("Misty"), zeke("Zeke");
Dog garbo("Garbo"), pooky("Pooky"), truffle("Truffle");
 
// Set up an array of pointers to animals, and set those pointers to our Cat and Dog objects
Animal *animals[] = { &fred, &garbo, &misty, &pooky, &truffle, &zeke };
for (int iii=0; iii < 6; ++iii)
    std::cout << animals[iii]->getName() << " says " << animals[iii]->speak() << '\n';

// Which produces the result:
//  Fred says Meow
//  Garbo says Woof
//  Misty says Meow
//  Pooky says Woof
//  Truffle says Woof
//  Zeke says Meow
```


> Even though these two examples only use Cat and Dog, any other classes we derive from Animal would also work with our report() function and animal array without further modification! This is perhaps the biggest benefit of virtual functions -- the ability to structure your code in such a way that newly derived classes will automatically work with the old code without modification!



## `Use of the virtual keyword`

> If a function is marked as virtual, all matching overrides are also considered virtual, even if they are not explicitly marked as such. However, having the keyword virtual on the derived functions does not hurt, and it serves as a useful reminder that the function is a virtual function rather than a normal one. Consequently, it’s generally a good idea to use the virtual keyword for virtualized functions in derived classes even though it’s not strictly necessary.



## `Return types of virtual functions`

> Under normal circumstances, the return type of a virtual function and its override must match.

```c
class Base
{
public:
    virtual int getValue() { return 5; }
};
 
class Derived: public Base
{
public:
    virtual double getValue() { return 6.78; }
};
```

> In this case, Derived::getValue() is not considered a matching override for Base::getValue() (it is considered a completely separate function).




## `Do not call virtual functions from constructors or destructors`

> Remember that when a Derived class is created, the Base portion is constructed first. If you were to call a virtual function from the Base constructor, and Derived portion of the class hadn’t even been created yet, it would be unable to call the Derived version of the function because there’s no Derived object for the Derived function to work on. In C++, it will call the Base version instead.

> A similar issue exists for destructors. If you call a virtual function in a Base class destructor, it will always resolve to the Base class version of the function, because the Derived portion of the class will already have been destroyed.

> **Rule**: Never call virtual functions from constructors or destructors




## `The downside of virtual functions`

> Since most of the time you’ll want your functions to be virtual, why not just make all functions virtual? The answer is because it’s inefficient -- resolving a virtual function call takes longer than resolving a regular one. Furthermore, the compiler also has to allocate an extra pointer for each class object that has one or more virtual functions. 


