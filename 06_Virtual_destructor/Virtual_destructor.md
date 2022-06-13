# Virtual destructor

## Factory pattern

```
class Dog
{
public:
	~Dog() { std::cout << "Dog destroyed!" << std::endl; }
};

class YellowDog: public Dog{
public:
	~YellowDog() { std::cout << "Yellow dog destroyed!" << std::endl; }
};

class DogFactory{
public:
	static Dog* createYellowDog()
	{
		return (new YellowDog());
	}
	// create other dogs
};

// --------------------------------------------------------------------

int main()
{
	Dog* pd = DogFactory::createYellowDog();
	// Do smth with pd
	delete pd;
	return 0;
}
```

The output is supposed to be `Dog destroyed!`

## Virtual destructor

`virtual ~Dog() { std::cout << "Dog destroyed!" << std::endl; }`

The output is 

```
Yellow dog destroyed!
Dog destroyed!
```

## Shared pointer

```
class Dog
{
public:
	~Dog() { std::cout << "Dog destroyed!" << std::endl; }
};

class YellowDog: public Dog{
public:
	~YellowDog() { std::cout << "Yellow dog destroyed!" << std::endl; }
};

class DogFactory{
public:
	static std::shared_ptr<Dog> createYellowDog()
	{
		return std::shared_ptr<YellowDog>(new YellowDog());
	}
	// create other dogs
};

// --------------------------------------------------------------------

int main()
{
	std::shared_ptr<Dog> pd = DogFactory::createYellowDog();
	// Do smth with pd
	return 0;
}
```

The output is 

```
Yellow dog destroyed!
Dog destroyed!
```

## virtual destructor
The default destructor of c++ is not a virtual function because virtual functions require additional virtual function tables and virtual table pointers, which occupy additional memory.

For classes that will not be inherited, if the destructor is a virtual function, it will waste memory. Therefore, the default destructor of c++ is not a virtual function, but a virtual function only when it needs to be used as a parent class

## factory pattern
The essence of the factory method pattern is to "define an interface for creating objects, but let the class implementing this interface decide which class to instantiate. **Factory methods delay the instantiation of classes to subclasses**."
