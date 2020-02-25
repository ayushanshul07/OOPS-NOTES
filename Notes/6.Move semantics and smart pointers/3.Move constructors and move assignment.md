#     Move constructors and move assignment
---

## `Copy constructors and copy assignment`

> Copy constructors are used to initialize a class by making a copy of an object of the same class. Copy assignment is used to copy one class to another existing class. By default, C++ will provide a copy constructor and copy assignment operator if one is not explicitly provided. These compiler-provided functions do shallow copies, which may cause problems for classes that allocate dynamic memory. So classes that deal with dynamic memory should override these functions to do deep copies.


```c
template<class T>
class Auto_ptr3
{
	T* m_ptr;
public:
	Auto_ptr3(T* ptr = nullptr)
		:m_ptr(ptr)
	{
	}
 
	~Auto_ptr3()
	{
		delete m_ptr;
	}
 
	// Copy constructor
	// Do deep copy of a.m_ptr to m_ptr
	Auto_ptr3(const Auto_ptr3& a)
	{
		m_ptr = new T;
		*m_ptr = *a.m_ptr;
	}
 
	// Copy assignment
	// Do deep copy of a.m_ptr to m_ptr
	Auto_ptr3& operator=(const Auto_ptr3& a)
	{
		// Self-assignment detection
		if (&a == this)
			return *this;
 
		// Release any resource we're holding
		delete m_ptr;
 
		// Copy the resource
		m_ptr = new T;
		*m_ptr = *a.m_ptr;
 
		return *this;
	}
 
	T& operator*() const { return *m_ptr; }
	T* operator->() const { return m_ptr; }
	bool isNull() const { return m_ptr == nullptr; }
};
 
class Resource
{
public:
	Resource() { std::cout << "Resource acquired\n"; }
	~Resource() { std::cout << "Resource destroyed\n"; }
};
 
Auto_ptr3<Resource> generateResource()
{
	Auto_ptr3<Resource> res(new Resource);
	return res; // this return value will invoke the copy constructor
}
 
int main()
{
	Auto_ptr3<Resource> mainres;
	mainres = generateResource(); // this assignment will invoke the copy assignment
    
    // This prints:
    // Resource acquired 
    // Resource acquired
    // Resource destroyed
    // Resource acquired
    // Resource destroyed
    // Resource destroyed
	return 0;
}
```

> **1**) Inside generateResource(), local variable res is created and initialized with a dynamically allocated Resource, which causes the first “Resource acquired”.
> **2**) Res is returned back to main() by value. We return by value here because res is a local variable -- it can’t be returned by address or reference because res will be destroyed when generateResource() ends. So res is copy constructed into a temporary object. Since our copy constructor does a deep copy, a new Resource is allocated here, which causes the second “Resource acquired”.
> **3**) Res goes out of scope, destroying the originally created Resource, which causes the first “Resource destroyed”.
> **4**) The temporary object is assigned to mainres by copy assignment. Since our copy assignment also does a deep copy, a new Resource is allocated, causing yet another “Resource acquired”.
> **5**) The assignment expression ends, and the temporary object goes out of expression scope and is destroyed, causing a “Resource destroyed”.
> **6**) At the end of main(), mainres goes out of scope, and our final “Resource destroyed” is displayed.


> Inefficient, but at least it doesn’t crash!




## `Move constructors and move assignment`

> C++11 defines two new functions in service of move semantics: a move constructor, and a move assignment operator. Whereas the goal of the copy constructor and copy assignment is to make a copy of one object to another, the goal of the move constructor and move assignment is to move ownership of the resources from one object to another (which is much less expensive than making a copy).

```c
#include <iostream>
 
template<class T>
class Auto_ptr4
{
	T* m_ptr;
public:
	Auto_ptr4(T* ptr = nullptr)
		:m_ptr(ptr)
	{
	}
 
	~Auto_ptr4()
	{
		delete m_ptr;
	}
 
	// Copy constructor
	// Do deep copy of a.m_ptr to m_ptr
	Auto_ptr4(const Auto_ptr4& a)
	{
		m_ptr = new T;
		*m_ptr = *a.m_ptr;
	}
 
	// Move constructor
	// Transfer ownership of a.m_ptr to m_ptr
	Auto_ptr4(Auto_ptr4&& a)
		: m_ptr(a.m_ptr)
	{
		a.m_ptr = nullptr; // we'll talk more about this line below
	}
 
	// Copy assignment
	// Do deep copy of a.m_ptr to m_ptr
	Auto_ptr4& operator=(const Auto_ptr4& a)
	{
		// Self-assignment detection
		if (&a == this)
			return *this;
 
		// Release any resource we're holding
		delete m_ptr;
 
		// Copy the resource
		m_ptr = new T;
		*m_ptr = *a.m_ptr;
 
		return *this;
	}
 
	// Move assignment
	// Transfer ownership of a.m_ptr to m_ptr
	Auto_ptr4& operator=(Auto_ptr4&& a)
	{
		// Self-assignment detection
		if (&a == this)
			return *this;
 
		// Release any resource we're holding
		delete m_ptr;
 
		// Transfer ownership of a.m_ptr to m_ptr
		m_ptr = a.m_ptr;
		a.m_ptr = nullptr; // we'll talk more about this line below
 
		return *this;
	}
 
	T& operator*() const { return *m_ptr; }
	T* operator->() const { return m_ptr; }
	bool isNull() const { return m_ptr == nullptr; }
};
 
class Resource
{
public:
	Resource() { std::cout << "Resource acquired\n"; }
	~Resource() { std::cout << "Resource destroyed\n"; }
};
 
Auto_ptr4<Resource> generateResource()
{
	Auto_ptr4<Resource> res(new Resource);
	return res; // this return value will invoke the move constructor
}
 
int main()
{
	Auto_ptr4<Resource> mainres;
	mainres = generateResource(); // this assignment will invoke the move assignment
 
    // This program prints:
    //    Resource acquired
    //    Resource destroyed
	
	return 0;
}
```


> **1**) Inside generateResource(), local variable res is created and initialized with a dynamically allocated Resource, which causes the first “Resource acquired”.
> **2**) Res is returned back to main() by value. Res is move constructed into a temporary object, transferring the dynamically created object stored in res to the temporary object. We’ll talk about why this happens below.
> **3**) Res goes out of scope. Because res no longer manages a pointer (it was moved to the temporary), nothing interesting happens here.
> **4**) The temporary object is move assigned to mainres. This transfers the dynamically created object stored in the temporary to mainres.
> **5**) The assignment expression ends, and the temporary object goes out of expression scope and is destroyed. However, because the temporary no longer manages a pointer (it was moved to mainres), nothing interesting happens here either.
> **6**) At the end of main(), mainres goes out of scope, and our final “Resource destroyed” is displayed.






## `When are the move constructor and move assignment called?`

> The move constructor and move assignment are called when those functions have been defined, and the argument for construction or assignment is an r-value. Most typically, this r-value will be a literal or temporary value.


> In most cases, a move constructor and move assignment operator will not be provided by default, unless the class does not have any defined copy constructors, copy assignment, move assignment, or destructors. However, the default move constructor and move assignment do the same thing as the default copy constructor and copy assignment (make copies, not do moves).


> **Rule**: If you want a move constructor and move assignment that do moves, you’ll need to write them yourself.





## `The key insight behind move semantics`

> If we construct an object or do an assignment where the argument is an l-value, the only thing we can reasonably do is copy the l-value. We can’t assume it’s safe to alter the l-value, because it may be used again later in the program. If we have an expression “a = b”, we wouldn’t reasonably expect b to be changed in any way.


> However, if we construct an object or do an assignment where the argument is an r-value, then we know that r-value is just a temporary object of some kind. Instead of copying it (which can be expensive), we can simply transfer its resources (which is cheap) to the object we’re constructing or assigning. This is safe to do because the temporary will be destroyed at the end of the expression anyway, so we know it will never be used again!





## `Move functions should always leave both objects in a well-defined state`

> In the above examples, both the move constructor and move assignment functions set a.m_ptr to nullptr. This may seem extraneous -- after all, if “a” is a temporary r-value, why bother doing “cleanup” if parameter “a” is going to be destroyed anyway?


> The answer is simple: When “a” goes out of scope, a’s destructor will be called, and a.m_ptr will be deleted. If at that point, a.m_ptr is still pointing to the same object as m_ptr, then m_ptr will be left as a dangling pointer. When the object containing m_ptr eventually gets used (or destroyed), we’ll get undefined behavior.


> Additionally, in the next lesson we’ll see cases where “a” can be an l-value. In such a case, “a” wouldn’t be destroyed immediately, and could be queried further before its lifetime ends.





## `Automatic l-values returned by value may be moved instead of copied`

> In the generateResource() function of the Auto_ptr4 example above, when variable res is returned by value, it is moved instead of copied, even though res is an l-value. The C++ specification has a special rule that says automatic objects returned from a function by value can be moved even if they are l-values. This makes sense, since res was going to be destroyed at the end of the function anyway! We might as well steal its resources instead of making an expensive and unnecessary copy.


> Although the compiler can move l-value return values, in some cases it may be able to do even better by simply eliding the copy altogether (which avoids the need to make a copy or do a move at all). In such a case, neither the copy constructor nor move constructor would be called.




## 
