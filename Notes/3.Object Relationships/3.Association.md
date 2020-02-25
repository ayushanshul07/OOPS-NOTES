#                                 Association
---

> Unlike object composition relationships, in an association, there is no implied whole/part relationship.


> To qualify as an association, an object and another object must have the following relationship:
>     **1**. The associated object (member) is otherwise unrelated to the object (class)
>     **2**. The associated object (member) can belong to more than one object (class) at a time
>     **3**. The associated object (member) does not have its existence managed by the object (class)
>     **4**. The associated object (member) may or may not know about the existence of the object (class)



## `Implementing associations`

> Because associations are a broad type of relationship, they can be implemented in many different ways. However, most often, associations are implemented using pointers, where the object points at the associated object.

```c
#include <iostream>
#include <string>
#include <vector>
 
// Since Doctor and Patient have a circular dependency, we're going to forward declare Patient
class Patient;
 
class Doctor
{
private:
	std::string m_name{};
	std::vector<Patient *> m_patient{};
 
public:
	Doctor(std::string name) :
		m_name(name)
	{
	}
 
	void addPatient(Patient *pat);
	
	// We'll implement this function below Patient since we need Patient to be defined at that point
	friend std::ostream& operator<<(std::ostream &out, const Doctor &doc);
 
	std::string getName() const { return m_name; }
};
 
class Patient
{
private:
	std::string m_name{};
	std::vector<Doctor *> m_doctor{}; // so that we can use it here
 
	// We're going to make addDoctor private because we don't want the public to use it.
	// They should use Doctor::addPatient() instead, which is publicly exposed
	void addDoctor(Doctor *doc)
	{
		m_doctor.push_back(doc);
	}
 
public:
	Patient(std::string name)
		: m_name(name)
	{
	}
 
	// We'll implement this function below Doctor since we need Doctor to be defined at that point
	friend std::ostream& operator<<(std::ostream &out, const Patient &pat);
 
	std::string getName() const { return m_name; }
 
	// We'll friend Doctor::addPatient() so it can access the private function Patient::addDoctor()
	friend void Doctor::addPatient(Patient *pat);
};
 
void Doctor::addPatient(Patient *pat)
{
	// Our doctor will add this patient
	m_patient.push_back(pat);
 
	// and the patient will also add this doctor
	pat->addDoctor(this);
}
 
std::ostream& operator<<(std::ostream &out, const Doctor &doc)
{
	unsigned int length = doc.m_patient.size();
	if (length == 0)
	{
		out << doc.m_name << " has no patients right now";
		return out;
	}
 
	out << doc.m_name << " is seeing patients: ";
	for (unsigned int count = 0; count < length; ++count)
		out << doc.m_patient[count]->getName() << ' ';
 
	return out;
}
 
std::ostream& operator<<(std::ostream &out, const Patient &pat)
{
	unsigned int length = pat.m_doctor.size();
	if (length == 0)
	{
		out << pat.getName() << " has no doctors right now";
		return out;
	}
 
	out << pat.m_name << " is seeing doctors: ";
	for (unsigned int count = 0; count < length; ++count)
		out << pat.m_doctor[count]->getName() << ' ';
 
	return out;
}
 
int main()
{
	// Create a Patient outside the scope of the Doctor
	Patient *p1 = new Patient("Dave");
	Patient *p2 = new Patient("Frank");
	Patient *p3 = new Patient("Betsy");
 
	Doctor *d1 = new Doctor("James");
	Doctor *d2 = new Doctor("Scott");
 
	d1->addPatient(p1);
 
	d2->addPatient(p1);
	d2->addPatient(p3);
 
	std::cout << *d1 << '\n';
	std::cout << *d2 << '\n';
	std::cout << *p1 << '\n';
	std::cout << *p2 << '\n';
	std::cout << *p3 << '\n';
 
	delete p1;
	delete p2;
	delete p3;
 
	delete d1;
	delete d2;
 
	return 0;
}
```


> In general, you should avoid bidirectional associations if a unidirectional one will do, as they add complexity and tend to be harder to write without making errors.



## `Reflexive association`

> Sometimes objects may have a relationship with other objects of the same type. This is called a **reflexive association**.



## `Composition vs aggregation vs association summary`

![Image](/home/sumit/Documents/medley/resources/rJxYKCp6Qr_BJdhbA6QB.png)
