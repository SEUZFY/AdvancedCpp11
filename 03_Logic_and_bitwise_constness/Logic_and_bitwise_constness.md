# Logic and bitwise constness

## Conflict in logic and bitwise constness

It is logically clear to programmer that `getItem()` is supposed to be const function.

However compiler disagrees.

```cpp
class BigArray{
	std::vector<int> v; // huge array
	int accessCounter;
public:
	int getItem(int index) const{
		accessCounter++;
		return v[index];
	}
};

... 
error: increment of member ‘BigArray::accessCounter’ in read-only object
   accessCounter++;

```

## Solution

### 1) Mutable keyword
```cpp
mutable int accessCounter;

/*
The keyword mutable is mainly used to allow a particular data member of const object to be modified. 
When we declare a function as const, the this pointer passed to function becomes const. 
Adding mutable to a variable allows a const pointer to change members.
*/
```

### 2) Const cast (should be avoided)

```cpp
	int getItem(int index) const{
		const_cast<BigArray*>(this)->accessCounter++;
		return v[index];
	}
```

## Another conflict

Compiler accepts a const specificator in `setAnotherVItem()`, but logically it is not right.

```
#include <iostream>
#include <vector>

class BigArray{
	std::vector<int> v; // huge array
	int accessCounter;
	int* another_v;
public:
	int getItem(int index) const{
		const_cast<BigArray*>(this)->accessCounter++;
		return v[index];
	}

	void setAnotherVItem(int index, int x) const
	{
		*(another_v + index) = x;
	}
};

// --------------------------------------------------------------------

int main()
{
	BigArray array;

	return 0;
}
```

## Solution

Just remove const in function

```
	void setAnotherVItem(int index, int x)
	{
		*(another_v + index) = x;
	}
```

## FUN

What the heck is this???

`const int* const fun (const int* const & p) const;`

### Explanation

1. the return value of fun is a constant pointer pointing to a constant integer value
2. the parameter of fun is a reference of a constant pointer pointing to a constant integer
the reference cannot refer to a different pointer (nature of references)
the referred pointer cannot point to a different value
the pointed value of the referred pointer cannot be changed
3. fun is also a const function, meaning that it cannot directly modify members unless they are marked mutable, also it can only call other const functions

### Reference to a pointer

* If a pointer is passed to a function as a parameter and tried to be modified 
  then the changes made to the pointer does not reflects back outside that function.

* This is because only a copy of the pointer is passed to the function. It can be said that **pass by pointer** is **passing a pointer by value**. 

* When you modify the pointer inside the function - instead of modifying the variable, you are only modifying a copy of the pointer 
  and the original pointer remains unmodified.
  
  ```cpp
  int global_Var = 42;
  
  /* function to change pointer value */
  void changePointerValue(int* p)
  {
      p = &global_Var;
  }
  
  int main()
  {
      int var = 23;
      int* ptr = &var;  // *ptr = 23 
      changePointerValue(ptr);  // *ptr is still 23
      return 0;
  }
  ```
* **Solution 1 - second pointer**
  ```cpp  
  void changePointerValue(int** second_ptr)
  {
    *second_ptr = &global_var;  // *second_ptr means the first ptr
  }
  
  int main()
  {
      int var = 23;
      int* ptr = &var;  // *ptr = 23 
      changePointerValue(&ptr);  // *ptr changed to the value of global_var
      return 0;
  }
* **Solution 2 - reference to a pointer**
  ```cpp  
  void changePointerValue(int*& ref_ptr)  // int*& NOT int&* : int* - the type, int*& - the reference of this type
  {
    ref_pre = &global_var;  // the reference is an alias
  }
  
  int main()
  {
      int var = 23;
      int* ptr = &var;  // *ptr = 23 
      changePointerValue(ptr);  // *ptr changed to the value of global_var
      return 0;
  }
  ```
  more info:
  https://www.geeksforgeeks.org/passing-reference-to-a-pointer-in-c/
