# Circular dependency issues with                                                                                                                        std::shared_ptr, and std::weak_ptr
---

> We saw how std::shared_ptr allowed us to have multiple smart pointers co-owning the same resource. However, in certain cases, this can become problematic.

```c
#include <iostream>
#include <memory> // for std::shared_ptr
#include <string>
 
class Person
{
	std::string m_name;
	std::shared_ptr<Person> m_partner; // initially created empty
 
public:
		
	Person(const std::string &name): m_name(name)
	{ 
		std::cout << m_name << " created\n";
	}
	~Person()
	{
		std::cout << m_name << " destroyed\n";
	}
 
	friend bool partnerUp(std::shared_ptr<Person> &p1, std::shared_ptr<Person> &p2)
	{
		if (!p1 || !p2)
			return false;
 
		p1->m_partner = p2;
		p2->m_partner = p1;
 
		std::cout << p1->m_name << " is now partnered with " << p2->m_name << "\n";
 
		return true;
	}
};
 
int main()
{
	auto lucy = std::make_shared<Person>("Lucy"); // create a Person named "Lucy"
	auto ricky = std::make_shared<Person>("Ricky"); // create a Person named "Ricky"
 
	partnerUp(lucy, ricky); // Make "Lucy" point to "Ricky" and vice-versa
 
	return 0;
}
```
