### C++函数模板的重载
当我们需要对不同的类型使用同一套函数逻辑时，就需要使用类模板，然而，有些类型不能使用同一种算法，需要单独处理，这时候就需要使用模板的重载。
比如这样一种情况：我们想要交换两个数组，在一般的模板入门中，一般会用引用或者指针实现，但是这样对于数组是不对的。
* 第一种方案：函数参数传入的是数组指针，指向数组的第0个元素，这样在交换时仅仅交换第一个值，而不是整个数组。
* 第二种方案：假设传入一个长度为5的int型数组，类型是int[5]，那么T换过来就是int[5] arrname，这是语法错误1；此外，数组名是一个常量，不允许修改，这是语法错误2。

```c++
// 重载示例
#include <iostream>
using namespace std;
template<class T> void Swap(T &a, T &b);  //模板①：交换基本类型的值
template<typename T> void Swap(T a[], T b[], int len);  //模板②：交换两个数组
void printArray(int arr[], int len);  //打印数组元素
int main(){
    //交换基本类型的值
    int m = 10, n = 99;
    Swap(m, n);  //匹配模板①
    cout<<m<<", "<<n<<endl;
    //交换两个数组
    int a[5] = { 1, 2, 3, 4, 5 };
    int b[5] = { 10, 20, 30, 40, 50 };
    int len = sizeof(a) / sizeof(int);  //数组长度
    Swap(a, b, len);  //匹配模板②
    printArray(a, len);
    printArray(b, len);
    return 0;
}
template<class T> void Swap(T &a, T &b){
    T temp = a;
    a = b;
    b = temp;
}
template<typename T> void Swap(T a[], T b[], int len){
    T temp;
    for(int i=0; i<len; i++){
        temp = a[i];
        a[i] = b[i];
        b[i] = temp;
    }
}
void printArray(int arr[], int len){
    for(int i=0; i<len; i++){
        if(i == len-1){
            cout<<arr[i]<<endl;
        }else{
            cout<<arr[i]<<", ";
        }
    }
}
```