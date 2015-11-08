#导读

1. default construtor
就是不使用任何参数就可以调用的构造函数（参数为空或全部都有默认值）。在C++11中新添加的特性：`default`,它的作用是：当用户自定义了构造函数，编译器就不会自为类生成默认的无参数构造函数，而我们如果想使用这个无参数构造函数，还要自己去写。并且写出来的勾走函数的效率没有编译器自动生成的效率要高。因此，可以使用如下方法：
```c++
class X{ 
 public: 
  X()= default; //默认构造函数
  X(int i){ 
    a = i; 
  }；

  X(int = 1) = default;   // 错误 , 默认构造函数 X(int=1) 含有默认参数  
  int f() = default;      // 错误 , 函数 f() 非类 X 的特殊成员函数  
 private: 
  int a; 
 }; 

 X x;
```

2. explicit关键字
explicit   只对构造函数起作用，用来抑制隐式转换。通过将构造函数声明为explicit（显式）的方式可以抑制隐式转换。也就是说，explicit构造函数必须显式调用。
```c++

```

3. delete关键字
使用`delete`关键字可以禁用函数，示例如下：

```c++
class X{
public:
    X();//default 构造函数
    X(const X&)=delete;  // 声明copy构造函数为 deleted 函数
    X& operator = (const X &) = delete; // 声明copy assignment操作符为 deleted 函数
    void doSomething(int x)=delete;
};

int main(){
    X x1;
    X x2(x1);   // 错误，拷贝构造函数被禁用
    X x3;
    x3 = x1;     // 错误，拷贝赋值操作符被禁用


    //注意，“=”是可以调用copy函数的
    X x4 = x1;//有新的对象x4被定义，因此这种写法是默认调用了类的拷贝函数
    X x4;
    x4 = x1; //没有新的对象定义，因此这种方式不是调用类的拷贝函数，而是使用赋值操作符“=”
    x4.doSomething(11);//被禁用的函数，不能调用只能
}
```

#条款03：尽可能使用const
`mutable`只能用于修饰类的非静态数据成员，将永远处于可变的状态，即使在一个const函数中。假如类的成员函数不会改变对象的状态，那么这个成员函数一般会声明为const。但是，有些时候，我们需要在const的函数里面修改一些跟类状态无关的数据成员，那么这个数据成员就应该被mutalbe来修饰。如下所示，为了统计对象输出次数，如果计数对象是普通变量的话，在const成员函数中是不能修改非const变量的；而该变量跟对象的状态无关，所以应该为了修改该变量只能去掉Output的const属性了。这时使用`mutable`就可以了。
```c++
class ClxTest
{
　public:
　　ClxTest();
　　~ClxTest();
 
　　void Output() const;
　　int GetOutputTimes() const;
 
　private:
　　mutable int m_iTimes;
};
 
ClxTest::ClxTest()
{
　m_iTimes = 0;
}
 
ClxTest::~ClxTest()
{}
 
void ClxTest::Output() const
{
　cout << "Output for test!" << endl;
　m_iTimes++;
}
 
int ClxTest::GetOutputTimes() const
{
　return m_iTimes;
}
 
void OutputTest(const ClxTest& lx)
{
　cout << lx.GetOutputTimes() << endl;
　lx.Output();
　cout << lx.GetOutputTimes() << endl;
}
```
当`const`与`non-const`成员函数有着实质等价的实现时，令`non-const`版本调用`const`版本可以避免代码重复。但是反过来是不行的。


#条款20：宁以pass-by-reference-to-const替换pas-by-value
在使用`pass-by-reference`时，声明为`const`是必要的,因为不这样做的话，在函数内部可能会修改传入的`reference`指向的对象

但是使用内置类型（如int），使用pass-by-value的效率要比`pass-by-reference`的效率要高。

`pass-by-value`意味着调用copy构造函数
```c++
bool Accept(Widget w);
...
Widget aWidget;
if(Accept(aWidget))...//aWidget调用Widget的copy函数复制到w体内
```