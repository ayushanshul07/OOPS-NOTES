#                         std::initializer_list
---


> If we want to initialize array with values, we can do so directly via the initializer list syntax:

```c
int main()
{
	int array[5] { 5, 4, 3, 2, 1 }; // initializer list
	for (int count=0; count < 5; ++count)
		std::cout << array[count] << ' ';
 
	return 0;
}
```


> This also works for dynamically allocated arrays:

```c
int main()
{
	int *array = new int[5] { 5, 4, 3, 2, 1 }; // initializer list
	for (int count = 0; count < 5; ++count)
		std::cout << array[count] << ' ';
	delete[] array;
 
	return 0;
}
```



> But there is a problem with user defined container class:
```c

#include <cassert> // for assert()
 
class IntArray
{
private:
    int m_length;
    int *m_data;
 
public:
    IntArray():
        m_length(0), m_data(nullptr)
    {
    }
 
    IntArray(int length):
        m_length(length)
    {
        m_data = new int[length];
    }
 
    ~IntArray()
    {
        delete[] m_data;
        // we don't need to set m_data to null or m_length to 0 here, since the object will be destroyed immediately after this function anyway
    }
 
    int& operator[](int index)
    {
        assert(index >= 0 && index < m_length);
        return m_data[index];
    }
 
    int getLength() { return m_length; }
};

int main()
{
	IntArray array { 5, 4, 3, 2, 1 }; // this line doesn't compile
	for (int count=0; count < 5; ++count)
		std::cout << array[count] << ' ';
 
	return 0;
}
```

> This code won’t compile, because the IntArray class doesn’t have a constructor that knows what to do with an initializer list.



## `Class initialization using std::initializer_list`

> When a C++11 compiler sees an initializer list, it automatically converts it into an object of type std::initializer_list. Therefore, if we create a constructor that takes a std::initializer_list parameter, we can create objects using the initializer list as an input. *std::initializer_list* lives in the *<initializer_list>* header.

```c
#include <cassert> // for assert()
#include <initializer_list> // for std::initializer_list
#include <iostream>
 
class IntArray
{
private:
	int m_length {};
	int *m_data {};
 
public:
	IntArray()
	{
	}
 
	IntArray(int length) :
		m_length(length)
	{
		m_data = new int[length];
	}
 
	IntArray(const std::initializer_list<int> &list) : // allow IntArray to be initialized via list initialization
		IntArray(static_cast<int>(list.size())) // use delegating constructor to set up initial array
	{
		// Now initialize our array from the list
		int count = 0;
		for (auto &element : list)
		{
			m_data[count] = element;
			++count;
		}
	}
 
	~IntArray()
	{
		delete[] m_data;
		// we don't need to set m_data to null or m_length to 0 here, since the object will be destroyed immediately after this function anyway
	}
 
	IntArray(const IntArray&) = delete; // to avoid shallow copies
	IntArray& operator=(const IntArray& list) = delete; // to avoid shallow copies
 
	int& operator[](int index)
	{
		assert(index >= 0 && index < m_length);
		return m_data[index];
	}
 
	int getLength() { return m_length; }
};
 
int main()
{
	IntArray array{ 5, 4, 3, 2, 1 }; // initializer list
	for (int count = 0; count < array.getLength(); ++count)
		std::cout << array[count] << ' ';
 
	return 0;
}
```


> One **caveat**: Initializer lists will always favor a matching initializer_list constructor over other potentially matching constructors. Thus, this variable definition: *intArray array { 5 };* would match to *IntArray(const std::initializer_list<int> &)*, not **IntArray(int)**. If you want to match to IntArray(int) once a initializer_list constructor has been defined, you’ll need to use copy initialization or direct initialization.



## `Class assignment using std::initializer_list`

> if you implement a constructor that takes a std::initializer_list, you should ensure you do at least one of the following:
>     **1**. Provide an overloaded list assignment operator
>     **2**. Provide a proper deep-copying copy assignment operator


> Here’s why: consider the above class (which doesn’t have an overloaded list assignment or a copy assignment), along with following statement:
> 	*array = { 1, 3, 5, 7, 9, 11 };* // overwrite the elements of array with the elements from the list


> First, the compiler will note that an assignment function taking a std::initializer_list doesn’t exist. Next it will look for other assignment functions it could use, and discover the implicitly provided copy assignment operator. However, this function can only be used if it can convert the initializer list into an IntArray. Because { 1, 3, 5, 7, 9, 11 } is a std::initializer_list, the compiler will use the list constructor to convert the initializer list into a temporary IntArray. Then it will call the implicit assignment operator, which will shallow copy the temporary IntArray into our array object.


> At this point, both the temporary IntArray’s m_data and array->m_data point to the same address (due to the shallow copy).


> At the end of the assignment statement, the temporary IntArray is destroyed. That calls the destructor, which deletes the temporary IntArray’s m_data. This leaves our array variable with a hanging m_data pointer. When you try to use array->m_data for any purpose (including when array goes out of scope and the destructor goes to delete m_data), you’ll get undefined results (and probably a crash).


> **Rule**: If you provide list construction, it’s a good idea to provide list assignment as well.

