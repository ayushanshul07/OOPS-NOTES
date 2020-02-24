#                 Basic inheritance in C++
---

> Inheritance in C++ takes place between classes. In an inheritance (is-a) relationship, the class being inherited from is called the **parent class**, **base clas**s, or **superclass**, and the class doing the inheriting is called the **child class**, **derived class**, or **subclass**.


> A child class inherits both behaviors (member functions) and properties (member variables) from the parent (subject to some access restrictions). These variables and functions become members of the derived class. Because child classes are full-fledged classes, they can (of course) have their own members that are specific to that class. 

```c
#include <string>

class Person
{
// In this example, we're making our members public for simplicity
public: 
    std::string m_name;
    int m_age;
 
    Person(std::string name = "", int age = 0)
        : m_name(name), m_age(age)
    {
    }
 
    std::string getName() const { return m_name; }
    int getAge() const { return m_age; }
 
};

// BaseballPlayer publicly inheriting Person
class BaseballPlayer : public Person
{
public:
    double m_battingAverage;
    int m_homeRuns;
 
    BaseballPlayer(double battingAverage = 0.0, int homeRuns = 0)
       : m_battingAverage(battingAverage), m_homeRuns(homeRuns)
    {
    }
};

// Employee publicly inherits from Person
class Employee: public Person
{
public:
    double m_hourlySalary;
    long m_employeeID;
 
    Employee(double hourlySalary = 0.0, long employeeID = 0)
        : m_hourlySalary(hourlySalary), m_employeeID(employeeID)
    {
    }
 
    void printNameAndSalary() const
    {
        std::cout << m_name << ": " << m_hourlySalary << '\n';
    }
};
```



> Inheriting from a base class means we don’t have to redefine the information from the base class in our derived classes. We automatically receive the member functions and member variables of the base class through inheritance, and then simply add the additional functions or member variables we want. This not only saves work, but also means that if we ever update or modify the base class (e.g. add new functions, or fix a bug), all of our derived classes will automatically inherit the changes!


