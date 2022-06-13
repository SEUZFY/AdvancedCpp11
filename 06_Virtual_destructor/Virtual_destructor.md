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

## 析构函数
C++默认的析构函数不是虚函数是因为虚函数需要额外的虚函数表和虚表指针，占用额外的内存。 

而对于不会被继承的类来说，其析构函数如果是虚函数，就会浪费内存。 因此C++默认的析构函数不是虚函数，而是只有当需要当作父类时，设置为虚函数.

## 工厂模式
工厂方法模式的实质是“定义一个创建对象的接口，但让实现这个接口的类来决定实例化哪个类。工厂方法让类的实例化推迟到子类中进行。”
