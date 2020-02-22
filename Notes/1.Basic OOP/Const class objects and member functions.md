#     Const class objects and member functions
---


## `Const Classes`


> Once a const class object has been initialized via constructor, any attempt to modify the member variables of the object is disallowed, as it would violate the const-ness of the object. This includes both changing member variables directly (if they are public), or calling member functions that set the value of member variables.

```c
class Something
{
public:
    int m_value;
 
    Something(): m_value(0) { }
 
    void setValue(int value) { m_value = value; }
    int getValue() { return m_value ; }
};
 
int main()
{
    const Something something; // calls default constructor
 
    something.m_value = 5; // compiler error: violates const
    something.setValue(5); // compiler error: violates const
 
    return 0;
}
```



## `Const member functions`


> Now, consider the following line of code:
	
```c
   std::cout << something.getValue();
```

> Perhaps surprisingly, this will also cause a compile error, even though getValue() doesn’t do anything to change a member variable! It turns out that **const class objects can only explicitly call const member functions**, and getValue() has not been marked as a const member function.


> A **const member function** is a member function that guarantees it will not modify the object or call any non-const member functions (as they may modify the object).

```c
class Something
{
public:
    int m_value;
 
    Something(): m_value(0) { }
 
    void resetValue() { m_value = 0; }
    void setValue(int value) { m_value = value; }
 
    int getValue() const { return m_value; } // note addition of const keyword after parameter list, but before function body
};
```


> For member functions defined outside of the class definition, the const keyword must be used on both the function prototype in the class definition and on the function definition:

```c
class Something
{
public:
    int m_value;
 
    Something(): m_value(0) { }
 
    void resetValue() { m_value = 0; }
    void setValue(int value) { m_value = value; }
 
    int getValue() const; // note addition of const keyword here
};
 
int Something::getValue() const // and here
{
    return m_value;
}
```


> **Note:** Constructors cannot be marked as const. This is because constructors need to be able to initialize their member variables, and a const constructor would not be able to do so. Consequently, the language disallows const constructors.


> **Rule**: Make any member function that does not modify the state of the class object const, so that it can be called by const objects


