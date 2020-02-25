#                    The hidden "this" pointer
---


## `Chaining member functions`


> It can sometimes be useful to have a class member function return the object it was working with as a return value. The primary reason to do this is to allow a series of member functions to be “chained” together, so several member functions can be called on the same object!


> By having functions that would otherwise return void return *this instead, you can make those functions chainable.
