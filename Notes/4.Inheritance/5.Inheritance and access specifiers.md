#             Inheritance and access specifiers
---

> Public members can be accessed by anybody. Private members can only be accessed by member functions of the same class or friends. This means derived classes can not access private members of the base class directly!



## `The protected access specifier`

> The **protected access specifier** allows the class the member belongs to, friends, and derived classes to access the member. However, protected members are not accessible from outside the class.

```c
class Base
{
public:
    int m_public; // can be accessed by anybody
private:
    int m_private; // can only be accessed by Base members and friends (but not derived classes)
protected:
    int m_protected; // can be accessed by Base members, friends, and derived classes
};
 
class Derived: public Base
{
public:
    Derived()
    {
        m_public = 1; // allowed: can access public base members from derived class
        m_private = 2; // not allowed: can not access private base members from derived class
        m_protected = 3; // allowed: can access protected base members from derived class
    }
};
 
int main()
{
    Base base;
    base.m_public = 1; // allowed: can access public members from outside class
    base.m_private = 2; // not allowed: can not access private members from outside class
    base.m_protected = 3; // not allowed: can not access protected members from outside class
}
```




## `So when should I use the protected access specifier?`

> With a protected attribute in a base class, derived classes can access that member directly. This means that if you later change anything about that protected attribute (the type, what the value means, etc…), you’ll probably need to change both the base class AND all of the derived classes.


> Therefore, using the protected access specifier is most useful when you (or your team) are going to be the ones deriving from your own classes, and the number of derived classes is reasonable. That way, if you make a change to the implementation of the base class, and updates to the derived classes are necessary as a result, you can make the updates yourself (and have it not take forever, since the number of derived classes is limited).


> Making your members private gives you better encapsulation and insulates derived classes from changes to the base class. But there’s also a cost to build a public or protected interface to support all the access methods or capabilities that the public and/or derived classes need. That’s additional work that’s probably not worth it, unless you expect someone else to be the one deriving from your class, or you have a huge number of derived classes, where the cost of updating them all would be expensive.




## `Different kinds of inheritance, and their impact on access`

> First, there are three different ways for classes to inherit from other classes: public, private, and protected.

```c
// Inherit from Base publicly
class Pub: public Base
{
};
 
// Inherit from Base privately
class Pri: private Base
{
};
 
// Inherit from Base protectedly
class Pro: protected Base
{
};
 
class Def: Base // Defaults to private inheritance
{
};
```

> If you do not choose an inheritance type, C++ defaults to private inheritance (just like members default to private access if you do not specify otherwise).


> When members are inherited, the access specifier for an inherited member may be changed (in the derived class only) depending on the type of inheritance used. Put another way, members that were public or protected in the base class may change access specifiers in the derived class.



## `Public inheritance`

> When you inherit a base class publicly, inherited public members stay public, and inherited protected members stay protected. Inherited private members, which were inaccessible because they were private in the base class, stay inaccessible.

```c
class Base
{
public:
    int m_public;
private:
    int m_private;
protected:
    int m_protected;
};
 
class Pub: public Base // note: public inheritance
{
    // Public inheritance means:
    // Public inherited members stay public (so m_public is treated as public)
    // Protected inherited members stay protected (so m_protected is treated as protected)
    // Private inherited members stay inaccessible (so m_private is inaccessible)
public:
    Pub()
    {
        m_public = 1; // okay: m_public was inherited as public
        m_private = 2; // not okay: m_private is inaccessible from derived class
        m_protected = 3; // okay: m_protected was inherited as protected
    }
};
 
int main()
{
    // Outside access uses the access specifiers of the class being accessed.
    Base base;
    base.m_public = 1; // okay: m_public is public in Base
    base.m_private = 2; // not okay: m_private is private in Base
    base.m_protected = 3; // not okay: m_protected is protected in Base
 
    Pub pub;
    pub.m_public = 1; // okay: m_public is public in Pub
    pub.m_private = 2; // not okay: m_private is inaccessible in Pub
    pub.m_protected = 3; // not okay: m_protected is protected in Pub

    return 0;
}
```


> **Rule**: Use public inheritance unless you have a specific reason to do otherwise.




## `Private inheritance`

> With private inheritance, all members from the base class are inherited as private. This means private members stay private, and protected and public members become private.


> Note that this does not affect the way that the derived class accesses members inherited from its parent! It only affects the code trying to access those members through the derived class.

```c
class Base
{
public:
    int m_public;
private:
    int m_private;
protected:
    int m_protected;
};
 
class Pri: private Base // note: private inheritance
{
    // Private inheritance means:
    // Public inherited members become private (so m_public is treated as private)
    // Protected inherited members become private (so m_protected is treated as private)
    // Private inherited members stay inaccessible (so m_private is inaccessible)
public:
    Pri()
    {
        m_public = 1; // okay: m_public is now private in Pri
        m_private = 2; // not okay: derived classes can't access private members in the base class
        m_protected = 3; // okay: m_protected is now private in Pri
    }
};
 
int main()
{
    // Outside access uses the access specifiers of the class being accessed.
    // In this case, the access specifiers of base.
    Base base;
    base.m_public = 1; // okay: m_public is public in Base
    base.m_private = 2; // not okay: m_private is private in Base
    base.m_protected = 3; // not okay: m_protected is protected in Base
 
    Pri pri;
    pri.m_public = 1; // not okay: m_public is now private in Pri
    pri.m_private = 2; // not okay: m_private is inaccessible in Pri
    pri.m_protected = 3; // not okay: m_protected is now private in Pri
```


> Private inheritance can be useful when the derived class has no obvious relationship to the base class, but uses the base class for implementation internally. In such a case, we probably don’t want the public interface of the base class to be exposed through objects of the derived class (as it would be if we inherited publicly).


> In practice, private inheritance is rarely used.




## `Protected inheritance`

> It is almost never used, except in very particular cases. With protected inheritance, the public and protected members become protected, and private members stay inaccessible.

