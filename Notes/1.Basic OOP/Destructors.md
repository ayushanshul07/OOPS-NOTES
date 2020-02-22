#                                 Destructors
---


> When an object goes out of scope normally, or a dynamically allocated object is explicitly deleted using the delete keyword, the class destructor is automatically called (if it exists) to do any necessary clean up before the object is removed from memory. 


> For simple classes (those that just initialize the values of normal member variables), a destructor is not needed because C++ will automatically clean up the memory for you. However, if your class object is holding any resources (e.g. dynamic memory, or a file or database handle), or if you need to do any kind of maintenance before the object is destroyed, the destructor is the perfect place to do so, as it is typically the last thing to happen before the object is destroyed.


> The destructor can not take arguments. This implies that only one destructor may exist per class, as there is no way to overload destructors since they can not be differentiated from each other based on arguments.


> Global variables are constructed before main() and destroyed after main().

```c
class Simple
{
private:
    int m_nID;
 
public:
    Simple(int nID)
    {
        std::cout << "Constructing Simple " << nID << '\n';
        m_nID = nID;
    }
 
    ~Simple()
    {
        std::cout << "Destructing Simple" << m_nID << '\n';
    }
 
    int getID() { return m_nID; }
};
 
int main()
{
    // Allocate a Simple on the stack
    Simple simple(1);
    std::cout << simple.getID() << '\n';
 
    // Allocate a Simple dynamically
    Simple *pSimple = new Simple(2);
    std::cout << pSimple->getID() << '\n';
    delete pSimple;
 
    return 0;
} // simple goes out of scope here

Simple simple(3);


This program produces the following result:
Constructing Simple 3
Constructing Simple 1
1
Constructing Simple 2
2
Destructing Simple2
Destructing Simple1
Destructing Simple3
```


> **RAII (Resource Acquisition Is Initialization)** is a programming technique whereby resource use is tied to the lifetime of objects with automatic duration (e.g. non-dynamically allocated objects). In C++, RAII is implemented via classes with constructors and destructors. 


> Under the RAII paradigm, objects holding resources should not be dynamically allocated. This is because destructors are only called when an object is destroyed. For objects allocated on the stack, this happens automatically when the object goes out of scope, so there’s no need to worry about a resource eventually getting cleaned up. However, for dynamically allocated objects, the user is responsible for deletion -- if the user forgets to do that, then the destructor will not be called, and the memory for both the class object and the resource being managed will be leaked!


> **Rule**: If your class dynamically allocates memory, use the RAII paradigm, and don’t allocate objects of your class dynamically


> **Note**: If you use the exit() function, your program will terminate and no destructors will be called.





