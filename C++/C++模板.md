# `C++`模板(泛型编程)

时刻牢记编译时，链接时，运行时！！还有静态和动态的区别！！

## 1. 理解泛型编程

泛型编程就是编写==与类型无关的通用型代码==，是代码复用的一种手段，`C++`中模板是实现泛型编程的一种手段。(通过模板达到对==参数类型==的控制，通过模板达到对==逻辑==的控制。)

## 2. 函数模板

```C++
# typename 也可以用 class 代替
template<typename T>
void Swap(T& left, T& right)
{
  T temp = left;
  left = right;
  right = temp;
}
```

### 1. 函数模板的理解

函数模板本身不是函数，就是一个模板。==在编译器编译阶段，会根据传入的实参类型推导生成对应类型的函数==。也就是说，模板就是把我们要干的活交给了编译器！让编译器去生成对应类型的函数。（**编译时动态生成的**)

### 2. 函数模板的实例化

模板的实例化就是==编译器根据实参类型推导对应类型函数的过程==。

![image-20231101173634078](E:\Note\C++\C++模板.assets\image-20231101173634078.png)

#### 1. 隐式实例化

就是编译器根据实参自动推导。

#### 2. 显式实例化

显式指定模板参数类型。

```C++
template<typename T>
void Swap(T& left, T& right)
{
  T temp = left;
  left = right;
  right = temp;
}
int main()
{
  int a = 1;
  double d = 3.3;
  // 显式指定
  Swap<int>(a, d);
  return 0;
}
```

### 3. 函数模板的匹配规则

如果**有显式实现的参数类型最匹配的函数，那就优先匹配显式实现的函数**，编译器不会再生成对应类型的函数。否则编译器才会生成对应类型的函数。

## 3. 类模板

```C++
template<class T1, class T2, ..., class Tn>
class 类名
{
	// 类内成员定义
};
```

==类模板必须显式的指定模板参数类型==，因为这个编译器无法自动推导！

```C++
// vector只是类名，vector<int>才是类型
vector<int> s1;
vector<double> s2;
```

#### ==取类模板内的未知类型==

==在类模板没有实例化之前不能使用类模板里的未知类型(如T这种模板类型)。==因为类模板没有实例化，它里面的类型也是**虚拟类型**，**编译器无法确定其具体类型**，导致后期无法处理！

```C++
template<class Container>
void printContainer(const Container& container)
{
  // 不行！Container类模板没有实例化之前它里面的类型是虚拟类型，
  // 编译器无法确定其具体类型
  // Container::const_iterator cit = contianer.begin();
  // 加 typename 修饰告诉编译器，等类模板实例化后再去获取类型！就可以了
  typename Container::const_iterator cit = contianer.begin();
  while(cit != container.end())
  {
    cout << *cit << " ";
    ++cit;
  }
  cout << endl;
}

int main()
{
  list<int> lt;
  lt.push_back(1);
  lt.push_back(2);
  lt.push_back(3);
  lt.push_back(4);
  printContainer(lt);
}
```

这时在该虚拟类型前==加 `typename` 修饰==，告诉编译器这是一个虚拟类型，等到类模板实例化后再去获取具体的类型。

## 4. 非类型模板参数	

模板参数分为==类型模板参数和非类型模板参数==。类型模板参数就是普通的在 `class/typename`后面的参数，由调用方指定参数类型，也可以给缺省值。而==非类型模板参数就是用一个常量作为模板参数==。

注意非类型模板参数必须是常量，在编译期间就能确认其取值！不能是浮点类型，字符串这些，基本只能是整型。

非类型模板参数可以使得代码更灵活，以前只能传类型，但是非类型模板参数==可以设置大小。==

```C++
// N 就是非类型模板参数，可以给缺省值
template<class T, size_t N = 10>
class Test
{
  // ...
}
```

## 5. 模板特化与偏特化

一般而言，模板就是为了实现**与类型无关**的代码。但是有时有些方法可能不能做到对所有的类型都适用，比如指针和引用类型。这时就要针对这些类型进行特殊的处理，也就是==针对这些类型实现一份特殊的方法---特化。== (模板的使用可以理解是==泛化==)

### 1. 函数模板特化

```C++
// 一个函数模板
template<class T>
bool Less(T left, T right)
{
	return left < right;
}
// 函数模板针对 int* 类型的特化
template<>
bool Less<int*>(int* left, int* right)
{
  return *left < *right;
}
// 不如直接针对该类型写一个函数！简单明了
bool Less(int* left, int* right)
{
  return *left < *right;
}

// 感觉这样更好！
template<class T>
bool Less(T* left, T* right)
{
  return *left < *right;
}
```

但是==一般而言需要函数模板特化时都是直接把这个函数再实现一下==，简单省事，代码可读性还好！！！所以基本不会用函数模板。

### 2. 类模板特化

就是针对类模板中特定的类型进行特殊化处理。类模板特化分为全特化和偏特化两种。

#### 1. 全特化

```C++
template<class T1, class T2>
class Data
{
public:
  Data() {cout<<"Data<T1, T2>" <<endl;}
private:
  T1 _d1;
  T2 _d2;
};
// 全特化，就是将所有模板参数特化
template<>                // 这里什么也不写
class Data<int, char>   	// 这里加上具体的类型
{	
public:
  Data() {cout<<"Data<int, char>" <<endl;}
private:
  int _d1;
  char _d2;
};
```

#### 2. 偏特化

==任何针对模版参数做进一步条件限制的都叫做偏特化。==一般有两种方式：一是**对部分模板参数特化处理**；二是**对参数做进一步的限制**，比如限制其为 类型 的指针或引用。

```C++
// 方式1：将部分参数特化
template <class T1>
class Data<T1, int>
{
public:
	Data() {cout<<"Data<T1, int>" <<endl;}
private:
  T1 _d1;
  int _d2;
};
// 方式2：对参数做进一步限制
template <class T1, class T2>
class Data <T1*, T2*>    // 限制两个模板参数都是 类型 的指针
{
public:
	Data() {cout<<"Data<T1*, T2*>" <<endl;}
private:
  T1 _d1;
	T2 _d2;
};
```



## 6. 模板分离编译的问题

一个程序是由很多源文件共同编译实现的，在编译时每个源文件都会生成一个目标文件，然后由链接器链接所有的目标文件生成可执行文件，这个过程称为分离编译！

但是模板分离编译会有问题！(就是在 `xxx.h` 文件中声明一个模板，然后在`xxx.cc`中定义该模板，在==另外一个 `xxx.cc`文件中使用模板==)

![image-20231102190352996](E:\Note\C++\C++模板.assets\image-20231102190352996.png)

会有链接错误！！**在模板定义的文件中不知道模板参数的具体类型**，所以不会实例化出真正可以调用的函数，符号表中也就没有函数地址！！所以在链接时找不到对应的函数地址。

模板分离编译问题的解决：

1. ==不要把 模板的定义 和 使用模板的函数 分到不同的文件中分开编译！==
2. 在模板定义的文件中显式的指定模板参数类型。



