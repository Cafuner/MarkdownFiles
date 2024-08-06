# C++知识



## 1. priority_queue

priority_queue的第三个参数是一个实现了函数运算符的类型。因此，可以是下面的这个结构体：

```c++
struct cmp
{
    bool operator() (const ListNode* a, const ListNode* b)
    {
        return a->val > b->val;
    }
};
```



## 2. 利用迭代器删除元素

(1）对于关联容器（如map, set, multimap, multiset)，删除当前的iterator，仅仅会使当前的iterator失效，只要在erase时，递增
当前的iterator即可获得下一个元素。这是因为map之类的容器，使用了红黑树来实现，插入，删除一个结点不会对其他结点造成影响。使用方式如下例子：

```C++
set<int> valset = {1, 2, 3, 4, 5, 6}
set<int>::iterator iter;
for (iter = valset.begin(); iter != valset.end(); )
{
	if (3 == *iter)
		valset.erase(iter++);
	else
		++iter;
}
```

(2）对于序列式容器（如vector，deque， list等），删除当前的iterator会使后面所有元素的iterator都失效。这是因为vector， deque
使用了连续分配的內存，删除一个元素导致后面所有的元素会向前移动一个位置。不过erase方法可以返回下一个有效的iterator。使用方
式如下,例如：

```c++
set<int> val = {1, 2, 3, 4, 5, 6}
set<int>::iterator iter;
for (iter = val.begin(); iter != val.end(); )
{
	if (3 == *iter)
		iter = val.erase(iter);
	else
		++iter;
}
```



## 3. C++中容器迭代器失效问题

迭代器本质就是一个指针，迭代器失效就是指针失效。在C++STL中，有的容器在插入元素后可能会调整元素的存储位置，这通常是基于数组的容器，例如vector，deque(非容器string同理)。因此，这类容器在插入元素或删除元素后，旧的迭代器可能就失效了。具体来讲，

对于链表和树形容器，由于其元素是分散存储的，插入和删除元素只是修改了指针的指向，因此，只有被删除元素的迭代器失效，其他迭代器不会被影响。





## 4. 左值和右值

在c++11之前，值类型分为左值和右值。左值是指程序员可以对其取地址的值，所以说左值绑定了内存中的某个对象，其余不可取地址的都是右值，例如数字字面量、临时对象等。

在c++11引入了移动语义之后，可以按照值的两个独立的属性划分种类：

1. 具名，也叫做有身份，即是否占用一定的内存，表示一个具体存在的实例对象；
2. 可移动，即是否可以转移自己的资源。

现在，根据这两个属性可以将所有值划分为3类：

1. 左值：具名且不可移动。他的定义与c++11以前的左值一样，就是可以取地址的值。
2. 纯右值：不具名且可移动。纯右值有两类，一类就是用于运算符的操作数，另一类就是用于初始化一个对象的值。由于纯右值不具名，所以程序员其实只能使用由纯右值初始化的具名对象。
3. 将亡值：具名且可移动。将亡值有两个来源：
    1. 来自于左值。因为左值是不可移动的，为了减少不必要的拷贝，有时候我们希望直接移动一个左值的资源，因此可以将一个左值转换为右值，这个右值就是将亡值。例如返回右值引用的函数的返回值，最典型的就是标准库里的move，其实就是将一个左值转换为右值引用，之后就可以转移这个左值的资源。
    2. 来自于纯右值。因为纯右值是不具名的，所以不能直接使用，例如临时对象。要使用临时对象，可以通过临时物化(temporary materialization)来让一个纯右值具名，具名后程序员就可以操作这块内存，这个值就变成了将亡值。典型的临时物化操作例如将一个纯右值绑定到一个右值引用，或者访问一个纯右值的非静态成员。

综上，我们把左值和将亡值统一称为泛左值，因为他们都绑定了一块程序员可以操作的内存。把纯右值和将亡值统一称为右值，因为他们都是可以移动的值。





## 5. decltype的推导过程

先做如下定义：

1. 简单表达式：不带括号的变量，函数参数，类成员
2. 复杂表达式：非简单表达式，例如 (x), (x.m_data), func()等

对于简单表达式，decltype(expr)的类型是expr的声明类型。

对于复杂表达式，假设expr的类型是T，那么decltype(expr)的类型是：

1. 若expr是prvalue，则类型是T；
2. 若expr是lvalue，则类型是T&；
3. 若expr是xvalue，则类型是T&&



## 6. 函数重载解析

1. 创建候选函数列表，包含名称与被调用函数相同的函数和模版函数。

2. 创建可行函数列表，即参数数目正确且类型正确的函数。

3. 选择最佳匹配执行。

    最佳到最差的匹配：

    1. 完全匹配。但常规函数优先于模板函数。
    2. 提升转换。例如char自动转换为int，float转换为double。
    3. 标准转换。例如int转换为char，long转换为double。
    4. 用户自定义的转换。例如在类中定义的转换。

>如果两个完全匹配的函数都是模版函数，那么更具体的优先。找出最具体的模版的规则称为部分排序规则（partial ordering rules）。



## 7. 通用引用

`&&` 并不是在所有情况下都代表右值引用。

**如果一个变量或参数，被声明为 T&& ，T 是某个被推导出的类型，那么这个变量或参数是通用引用。**

注意两个条件：

1. 类型必须是 T&&， 任何其他限定符都不能出现，例如 const T&& 则是常量右值引用。
2. T必须经过类型推导。例如在类模版中，如果T是依赖于类模版的模版参数，那么编译到T&&时其实T的类型已经确定，不会再推导，那么T&&就是右值引用，而不是通用引用。



## 8. 函数模版类型推导

假设对于函数模版：

```c++
template <typename T>
void func(ParamType param);
```

调用如下：

```c++
func(expr);  
```

在编译期间，编译器使用`expr`进行两个类型推导：一个是针对`T`的，另一个是针对`ParamType`的。这两个类型通常是不同的，因为`ParamType`包含一些修饰，比如`const`和引用修饰符。

**总体原则：先推导出T的类型，然后代入ParamType进行引用折叠，推导出param的类型。**

具体的，有如下3种情况：

### 1. ParamType是指针或引用，但不是通用引用

```c++
template <typename T>
void func(T& param);

template <typename T>
void func(const T&& param); // 这里因为存在const限定符，T不是通用引用
```

在这种情况下，类型推导规则为：

1. 忽略expr的引用性（reference-ness)。即如果`expr`的类型是一个引用，忽略引用部分。
2. 然后`expr`的类型与`ParamType`进行模式匹配来决定`T`。

也就是说，如果不是通用引用，那么最终T只保留原始类型，而param则一定是所声明的引用类型。另外，T的const性质也会保留（除非模版中有const，那么T就没有const）。

>为什么需要推导T，为什么不直接代入expr并进行引用折叠决定ParamType呢？
>
>如果在于，如果直接将expr代入T，那么T的引用性就与调用时传递的实参相同。那么如果传递一个引用，在模版内部就无法获得原始类型，因为T也是引用类型，从而无法创建原始类型的对象。



### 2. ParamType是通用引用

```c++
template <typename T>
void func(T&& param);
```

在这种情况下，类型推导规则为：

1. 忽略expr的引用性。（对于将亡值没有引用性，绑定到右值引用后的变量才有引用性）
2. 如果expr是左值，则 T 的类型为原始类型的左值引用。
3. 如果expr是右值，则 T 的类型为原始类型。

另外，T的const性质也会保留。

经过引用折叠，param要么为左值引用(T为左值引用)，要么为右值引用(T为原始类型)。

```c++
template <typename T> void func(T &&t);

int main() {
  int i = 0;
  int& ir1 = i;
  int&& ir2 = 10;
  const int ci = 100;
  const int &cir = ci;
  func(i);            // void func<int &>(int &t)
  func(ir1);          // void func<int &>(int &t)
  func(ir2);          // void func<int &>(int &t)
  func(std::move(i)); // void func<int>(int &&t)
  func(ci);           // void func<const int &>(const int &t)
  func(cir);          // void func<const int &>(const int &t)
}
```



### 3. ParamType不是指针也不是引用

这种情况下，函数使用值传递的方式传参。这意味着无论传递什么`param`都会成为它的一份拷贝。

由于值传递，所以T的引用性，const，volatile都会忽略。

```c++
int x=27;                       //如之前一样
const int cx=x;                 //如之前一样
const int & rx=cx;              //如之前一样

f(x);                           //T和param的类型都是int
f(cx);                          //T和param的类型都是int
f(rx);                          //T和param的类型都是int
```

虽然实参cx和rx有常量性，但是T却没有，因为param是一份拷贝，无论如何也不会修改到cx和rx。

但是另外要注意，只有顶层const会被忽略。如果传递的是指向常量的指针，那么T仍然是指向常量的指针。



### 数组实参

大多数情况下，数组类型和指针类型可以互换，但是在推导类型时，有所不同。

1. 如果函数模版的形参是传值的，那么T会被推导为指针类型。

```c++
const char name[] = "Hello world";

template <typename T>
void func(T param);

f(name); // T 被推导为 const char*

```

2. 如果形参是引用，神奇的是，T会被推导为真正的数组。

```c++
template <typename T>
void func(T& param); // T被推导 const char[13], param的类型是 const char (&)[13]
```

有趣的是，可声明指向数组的引用的能力，使得我们可以创建一个模板函数来推导出数组的大小。

```
//在编译期间返回一个数组大小的常量值（//数组形参没有名字，
//因为我们只关心数组的大小）
template<typename T, std::size_t N>                    
constexpr std::size_t arraySize(T (&)[N]) noexcept     
{                                                      
    return N;                                          
}                                                      

```

函数声明为`constexpr`使得结果在编译期间可用。这使得我们可以用一个花括号声明一个数组，然后第二个数组可以使用第一个数组的大小作为它的大小，就像这样：

```c++
int keyVals[] = { 1, 3, 7, 9, 11, 22, 35 };             //keyVals有七个元素

int mappedVals[arraySize(keyVals)];                     //mappedVals也有七个
```



### 函数实参

在C++中不只是数组会退化为指针，函数类型也会退化为一个函数指针，我们对于数组类型推导的全部讨论都可以应用到函数类型推导和退化为函数指针上来。结果是：

```c++
void someFunc(int, double);         //someFunc是一个函数，
                                    //类型是void(int, double)

template<typename T>
void f1(T param);                   //传值给f1

template<typename T>
void f2(T & param);                 //传引用给f2

f1(someFunc);                       //param被推导为指向函数的指针，
                                    //类型是void(*)(int, double)
f2(someFunc);                       //param被推导为指向函数的引用，
                                    //类型是void(&)(int, double)
```



总结下：

- 在模板类型推导时，有引用的实参会被视为无引用，他们的引用会被忽略
- 对于通用引用的推导，左值实参会被特殊对待
- 对于传值类型推导，`const`和/或`volatile`实参会被认为是non-`const`的和non-`volatile`的
- 在模板类型推导时，数组名或者函数名实参会退化为指针，除非它们被用于初始化引用



## 9. auto类型推导

auto类型推导和模板类型推导有一个直接的映射关系，除了一个例外。

首先来看如何利用模版类型推导的规则来进行auto类型推导。

每一个auto申明都必须被一个表达式初始化。我们假设存在一个函数模版，其模版参数T就是auto，其形参就是auto的类型说明符，使用初始化表达式调用这个函数模版，模版推导出的T的类型就是auto的类型。

唯一的区别在于：

当用`auto`声明的变量使用花括号进行初始化，`auto`类型推导推出的类型则为`std::initializer_list<T>`，接着会发生第二次类型推导，即使用花括号中的元素推导T。第二次推导的规则与模版类型推导相同。

但模板类型推导并不认识花括号。

```c++
auto x = { 11, 23, 9 };         //x的类型是std::initializer_list<int>

template<typename T>            //带有与x的声明等价的
void f(T param);                //形参声明的模板

f({ 11, 23, 9 });               //错误！不能推导出T
```

然而如果在模板中指定`T`是`std::initializer_list<T>`而留下未知`T`,模板类型推导就能正常工作：

```c++
template<typename T>
void f(std::initializer_list<T> initList);

f({ 11, 23, 9 });               //T被推导为int，initList的类型为
                                //std::initializer_list<int>
```

因此`auto`类型推导和模板类型推导的真正区别在于，`auto`类型推导假定花括号表示`std::initializer_list`而模板类型推导不会这样。

另外，C++14允许`auto`用于函数返回值并会被推导，而且C++14的*lambda*函数也允许在形参声明中使用`auto`。但是在这些情况下`auto`实际上使用**模板类型推导**的那一套规则在工作，而不是`auto`类型推导，所以说下面这样的代码不会通过编译：

```c++
auto createInitList()
{
    return { 1, 2, 3 };         //错误！不能推导{ 1, 2, 3 }的类型
}
```













