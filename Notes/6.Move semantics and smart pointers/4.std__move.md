#                                    std::move
---

> Once you start using move semantics more regularly, you’ll start to find cases where you want to invoke move semantics, but the objects you have to work with are l-values, not r-values. 

```c
#include <iostream>
#include <string>
 
template<class T>
void myswap(T& a, T& b) 
{ 
  T tmp { a }; // invokes copy constructor
  a = b; // invokes copy assignment
  b = tmp; // invokes copy assignment
}
 
int main()
{
	std::string x{ "abc" };
	std::string y{ "de" };
 
	std::cout << "x: " << x << '\n';
	std::cout << "y: " << y << '\n';
 
	myswap(x, y);
 
	std::cout << "x: " << x << '\n';
	std::cout << "y: " << y << '\n';
 
	return 0;
}
```

> This version of swap makes 3 copies. That leads to a lot of excessive string creation and destruction, which is slow. However, doing copies isn’t necessary here. All we’re really trying to do is swap the values of a and b, which can be accomplished just as well using 3 moves instead! So if we switch from copy semantics to move semantics, we can make our code more performant. But how? The problem here is that parameters a and b are l-value references, not r-value references, so we don’t have a way to invoke the move constructor and move assignment operator instead of copy constructor and copy assignment. By default, we get the copy constructor and copy assignment behaviors.





## `std::move`

> In C++11, std::move is a standard library function that serves a single purpose -- to convert its argument into an r-value. We can pass an l-value to std::move, and it will return an r-value reference. std::move is defined in the utility header.

```c
#include <iostream>
#include <string>
#include <utility> // for std::move
 
template<class T>
void myswap(T& a, T& b) 
{ 
  T tmp { std::move(a) }; // invokes move constructor
  a = std::move(b); // invokes move assignment
  b = std::move(tmp); // invokes move assignment
}
 
int main()
{
	std::string x{ "abc" };
	std::string y{ "de" };
 
	std::cout << "x: " << x << '\n';
	std::cout << "y: " << y << '\n';
 
	myswap(x, y);
 
	std::cout << "x: " << x << '\n';
	std::cout << "y: " << y << '\n';
 
	return 0;
}
```





## `Another example`

> We can also use std::move when filling elements of a container, such as std::vector, with l-values.

```c
#include <iostream>
#include <string>
#include <utility> // for std::move
#include <vector>
 
int main()
{
	std::vector<std::string> v;
	std::string str = "Knock";
 
	std::cout << "Copying str\n";
	v.push_back(str); // calls l-value version of push_back, which copies str into the array element
	
	std::cout << "str: " << str << '\n';
	std::cout << "vector: " << v[0] << '\n';
 
	std::cout << "\nMoving str\n";
 
	v.push_back(std::move(str)); // calls r-value version of push_back, which moves str into the array element
	
	std::cout << "str: " << str << '\n';
	std::cout << "vector:" << v[0] << ' ' << v[1] << '\n';
 
	return 0;
}
```

> In the first case, we passed push_back() an l-value, so it used copy semantics to add an element to the vector. For this reason, the value in str is left alone.

> In the second case, we passed push_back() an r-value (actually an l-value converted via std::move), so it used move semantics to add an element to the vector. This is more efficient, as the vector element can steal the string’s value rather than having to copy it. In this case, str is left empty.

> At this point, it’s worth reiterating that std::move() gives a hint to the compiler that the programmer doesn’t need this object any more (at least, not in its current state). Consequently, you should not use std::move() on any persistent object you don’t want to modify, and you should not expect the state of any objects that have had std::move() applied to be the same after they are moved!





## `Move functions should always leave your objects in a well-defined state`

> It’s a good idea to always leave the objects being stolen from in some well-defined (deterministic) state. Ideally, this should be a “null state”, where the object is set back to its uninitiatized or zero state. Now we can talk about why: with std::move, the object being stolen from may not be a temporary after all. The user may want to reuse this (now empty) object again, or test it in some way, and can plan accordingly.





## `Where else is std::move useful?`

> std::move can also be useful when sorting an array of elements. Many sorting algorithms (such as selection sort and bubble sort) work by swapping pairs of elements. In previous lessons, we’ve had to resort to copy-semantics to do the swapping. Now we can use move semantics, which is more efficient. It can also be useful if we want to move the contents managed by one smart pointer to another.












