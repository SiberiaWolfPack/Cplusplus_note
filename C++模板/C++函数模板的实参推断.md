### C++函数模板的实参推断
类模板使用的时候需要显式的指定实参类型，而函数模板则不需要。这种通过函数实参来确定模板实参的过程就是模板实参推断。

#### 模板实参推断中的类型转换
对于普通函数来说，发生函数调用时会对实参的类型进行适当的转换，以适应形参的类型，比如：
* 算数转换：例如 int 转换为 float，char 转换为 int，double 转换为 int 等。
* 派生类向基类的转换：也就是向上转型。
* const 转换：也即将非 const 类型转换为 const 类型，例如将 char * 转换为 const char *。
* 数组或函数指针转换：如果函数形参不是引用类型，那么数组名会转换为数组指针，函数名也会转换为函数指针。
* 用户自定的类型转换。

而对于函数模板，仅有const转换和数组或函数指针转换。比如以下几个模板：
```c++
template<typename T> void func1(T a, T b);
template<typename T> void func2(T *buffer);
template<typename T> void func3(const T &stu);
template<typename T> void func4(T a);
template<typename T> void func5(T &a);

// 具体调用形式
int name[20];
Student stu1("张华", 20, 96.5);  // 创建一个Student类型的对象
func1(12.5, 30);  // Error，无法进行实参推断
func2(name);  // name的类型从 int [20] 换转换为 int *，所以 T 的真实类型为 int（这里要搞清楚T到底是什么类型）
func3(stu1);  // 非const转换为const，T 的真实类型为 Student
func4(name);  // name的类型从 int [20] 换转换为 int *，所以 T 的真实类型为 int *
func5(name);  // name的类型依然为 int [20]，不会转换为 int *，所以 T 的真实类型为 int [20]
```

要注意这样的函数模板：`template<typename T> void func(T &a, T &b);`引用类型的参数。如果你去这样调用：
```c++
int a1[10],a2[20];
func(a1,a2);
```
就会出错，因为这里数组无法转变为指针，推断结果不知道是int[10]还是int[20]，导致调用失败。

#### 为函数模板显式地指明实参
如果有多个类型参数，编译器可能无法推断出具体类型，比如：
```c++
template <typename T1,typename T2>
void fun(T1 a)
{
	T2 b;
	...
}
```
这时候只能推断出a的类型而无法推断出b的类型。有两种方法解决：
* 调用时全部指定，比如:`fun<int,int>(10);`
* 只指定某些参数，其他的推断得到，注意T1,T2...的匹配顺序和指定顺序一致，比如改写为：
```c++
template <typename T1,typename T2>
void fun(T2 a)
{
	T1 b;
	...
}

func<int>(10); // 指定的是T2
```
如果我们显式地指明实参类型，那么上述的所有转换都能发生。