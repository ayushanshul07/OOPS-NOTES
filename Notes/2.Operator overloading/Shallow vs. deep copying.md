#                     Shallow vs. deep copying
---

## `Shallow copying`

> Because C++ does not know much about your class, the default copy constructor and default assignment operators it provides use a copying method known as a **memberwise copy** (also known as a **shallow copy**). This means that C++ copies each member of the class individually (using the assignment operator for overloaded operator=, and direct initialization for the copy constructor).


> When designing classes that handle dynamically allocated memory, memberwise (shallow) copying can get us in a lot of trouble! This is because shallow copies of a pointer just copy the address of the pointer -- it does not allocate any memory or copy the contents being pointed to!

```c
#include <cstring> // for strlen()
#include <cassert> // for assert()
 
class MyString
{
private:
    char *m_data;
    int m_length;
 
public:
    MyString(const char *source="")
    {
        assert(source); // make sure source isn't a null string
 
        // Find the length of the string
        // Plus one character for a terminator
        m_length = std::strlen(source) + 1;
        
        // Allocate a buffer equal to this length
        m_data = new char[m_length];
        
        // Copy the parameter string into our internal buffer
        for (int i=0; i < m_length; ++i)
            m_data[i] = source[i];
    
        // Make sure the string is terminated
        m_data[m_length-1] = '\0';
    }
 
    ~MyString() // destructor
    {
        // We need to deallocate our string
        delete[] m_data;
    }
 
    char* getString() { return m_data; }
    int getLength() { return m_length; }
};

int main()
{
    MyString hello("Hello, world!");
    {
        MyString copy = hello; // use default copy constructor
    } // copy is a local variable, so it gets destroyed here.  The destructor deletes copy's string, which leaves hello with a dangling pointer
 
    std::cout << hello.getString() << '\n'; // this will have undefined behavior
 
    return 0;
}
```



## `Deep copying`

> A **deep copy** allocates memory for the copy and then copies the actual value, so that the copy lives in distinct memory from the source. This way, the copy and source are distinct and will not affect each other in any way. Doing deep copies requires that we write our own copy constructors and overloaded assignment operators.

```c
// assumes m_data is initialized
void MyString::deepCopy(const MyString& source)
{
    // first we need to deallocate any value that this string is holding!
    delete[] m_data;
 
    // because m_length is not a pointer, we can shallow copy it
    m_length = source.m_length;
 
    // m_data is a pointer, so we need to deep copy it if it is non-null
    if (source.m_data)
    {
        // allocate memory for our copy
        m_data = new char[m_length];
 
        // do the copy
        for (int i=0; i < m_length; ++i)
            m_data[i] = source.m_data[i];
    }
    else
        m_data = nullptr;
}
 
// Copy constructor
MyString::MyString(const MyString& source):
    m_data(nullptr)
{
    deepCopy(source);
}

// Assignment operator
MyString& MyString::operator=(const MyString & source)
{
    // check for self-assignment
    if (this == &source)
        return *this;
 
    // now do the deep copy
    deepCopy(source);
 
    return *this;
}
```
