##什么是回调函数
回调函数就是一个通过函数指针调用的函数。如果你把函数的指针（地址）作为参数传递给另一个函数，当这个指针被用为调用它所指向的函数时，我们就说这是回调函数。
知乎上有一个形象的比喻：
>你到一个商店买东西，刚好你要的东西没有货，于是你在店员那里留下了你的电话，过了几天店里有货了，店员就打了你的电话，然后你接到电话后就到店里去取了货。在这个例子里，你的电话号码就叫回调函数，你把电话留给店员就叫登记回调函数，店里后来有货了叫做触发了回调关联的事件，店员给你打电话叫做调用回调函数，你到店里去取货叫做响应回调事件。

##为什么要使用回调函数？

因为可以把调用者与被调用者分开。调用者不关心谁是被调用者，所有它需知道的，只是存在一个具有某种特定原型、某些限制条件（如返回值为int）的被调用函数。

　回调可用于通知机制，例如，有时要在程序中设置一个计时器，每到一定时间，程序会得到相应的通知，但通知机制的实现者对我们的程序一无所知。而此时，就需有一个特定原型的函数指针，用这个指针来进行回调，来通知我们的程序事件已经发生。实际上，SetTimer() API使用了一个回调函数来通知计时器，而且，万一没有提供回调函数，它还会把一个消息发往程序的消息队列。

##函数指针定义：
定义一个函数的指针：
```c++
void f();//函数原型
void (*)()//函数指针
```

左边圆括弧中的星号是函数指针声明的关键。另外两个元素是函数的返回类型（void）和由边圆括弧中的入口参数（本例中参数是空）。本例中还没有创建指针变量，只是声明了变量类型

位函数指针声明类型定义：
```c++
typedef void(*pfv)();

void func()
{
    /*do something*/
}
ptv=func;
```

pfv是一个函数指针，它指向的函数没有输入参数，返回类行为void。使用这个类型定义名可以隐藏复杂的函数指针语法。 

现在可以将pfv传递给一个函数的调用者-caller(),它将调用pfv指向的函数，而此函数名是未知的： 
```c++
void caller(void(*ptr)()) 
{ 
    ptr(); /* 调用ptr指向的函数 */ 
} 
void func(); 
int main() 
{ 
    pfv = func; 
    caller(pfv); /* 传递函数地址到调用者 */ 
} 
```

如果赋了不同的值给p（不同函数地址），那么调用者将调用不同地址的函数。赋值可以发生在运行时，这样使你能实现动态绑定。 