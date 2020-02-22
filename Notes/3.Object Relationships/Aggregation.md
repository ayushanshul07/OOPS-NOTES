#                                 Aggregation
---

## `Aggregation`

> To qualify as an aggregation, a whole object and its parts must have the following relationship:
>     **1**. The part (member) is part of the object (class)
>     **2**. The part (member) can belong to more than one object (class) at a time
>     **3**. The part (member) does not have its existence managed by the object (class)
>     **4**. The part (member) does not know about the existence of the object (class)



## `Implementing aggregations`

> In an aggregation, we also add parts as member variables. However, these member variables are typically either references or pointers that are used to point at objects that have been created outside the scope of the class. Consequently, an aggregation usually either takes the objects it is going to point to as constructor parameters, or it begins empty and the subobjects are added later via access functions or operators.


> Because these parts exist outside of the scope of the class, when the class is destroyed, the pointer or reference member variable will be destroyed (but not deleted). Consequently, the parts themselves will still exist.

```c
#include <string>
#include <iostream>
 
class Teacher
{
private:
    std::string m_name;
 
public:
    Teacher(std::string name)
        : m_name(name)
    {
    }
 
    std::string getName() { return m_name; }
};
 
class Department
{
private:
    Teacher *m_teacher; // This dept holds only one teacher for simplicity, but it could hold many teachers
 
public:
    Department(Teacher *teacher = nullptr)
        : m_teacher(teacher)
    {
    }
};
 
int main()
{
    // Create a teacher outside the scope of the Department
    Teacher *teacher = new Teacher("Bob"); // create a teacher
    {
        // Create a department and use the constructor parameter to pass
        // the teacher to it.
        Department dept(teacher);
 
    } // dept goes out of scope here and is destroyed
 
    // Teacher still exists here because dept did not delete m_teacher
 
    std::cout << teacher->getName() << " still exists!";
 
    delete teacher;
 
    return 0;
}
```


