#                     Overloading typecasts
---


```c
class Cents
{
private:
    int m_cents;
public:
    Cents(int cents=0)
    {
        m_cents = cents;
    }
 
    // Overloaded int cast
    operator int() const { return m_cents; }
 
    int getCents() const { return m_cents; }
    void setCents(int cents) { m_cents = cents; }
};
```


> There are three things to note:
> **1.** To overload the function that casts our class to an int, we write a new function in our class          called operator int(). Note that there is a space between the word operator and the type we are casting to.
> **2.** User-defined conversions do not take parameters, as there is no way to pass arguments to them.
> **3.** User-defined conversions do not have a return type. C++ assumes you will be returning the correct type.


```c
class Cents
{
private:
	int m_cents;
public:
	Cents(int cents = 0)
	{
		m_cents = cents;
	}
 
	// Overloaded int cast
	operator int() const { return m_cents; }
 
	int getCents() const { return m_cents; }
	void setCents(int cents) { m_cents = cents; }
};
 
class Dollars
{
private:
    int m_dollars;
public:
    Dollars(int dollars=0)
    {
        m_dollars = dollars;
    }
 
     // Allow us to convert Dollars into Cents
     operator Cents() const { return Cents(m_dollars * 100); }
};
 
void printCents(Cents cents)
{
    std::cout << cents; // cents will be implicitly cast to an int here
}
 
int main()
{
    Dollars dollars(9);
    printCents(dollars); // dollars will be implicitly cast to a Cents here
 
    return 0;
}
```
