#JavaScript学习笔记
##变量
###变量查看
在chrome浏览器中，可以使用console.log(变量名)来查看指定变量的值。
###变量声明
变量名可以是字母，数字，符号的组成。不能以数字开头。变量的赋值类型可以是布尔值，字符串，Number等，只能声明一次，之后可以赋值任何类型的值。
**NOTE：**JavaScript把null、undefined、0、NaN和空字符串''视为false，其他值一概视为true
```javascript
var a;
var m2=true;
var m_2d='007';
```
这种变量类型不固定的语言称为动态语言。JavaScript,Python等都属于动态语言。。静态语言在定义变量时必须指定变量类型，如果赋值的时候类型不匹配，就会报错。例如Java是静态语言。
>一个变量若没有通过`var`声明变量，则该变量自动声明为全局变量。

strict模式下，如果没有使用var声明变量，是会报错的。启用方式为`use strict`
###字符串
字符串使用`''或""`包裹内容，如果字符串中既包含`'`,又包含`"`,则可以使用转义字符，如`I\'m \"OK\"!'`表示的内容为`I'm "OK"!`
**需要注意的是：**可以为字符串变量赋值，但是不会起作用，如：
```javascript
var s='Test';
s[0]='X';
alert(s);
```
显示还是Test而不是Xest.
**需要注意的是：**JavaScript提供的一系列字符串处理函数不会改变原来的字符串变量，而是会返回一个新的字符串变量。

+ toUpperCase():将字符串全部转换为大写
```javascript
var s='hello';
s.toUpperCase();
```
+ toLowerCase():将字符串全部转换为小写
+ indexOf():会搜索指定字符串出现的位置
```javascript
var s='helloWorld';
s.toUpperCase('World');
```
+ substring():返回指定索引区间的字符串

###数组
要访问数组长度，直接访问数组的length属性即可。`s.length`
给数组的长度赋值会有以下变化要注意：
```javascript
var arr = [1, 2, 3];
arr.length; // 3
arr.length = 6;
arr; // arr变为[1, 2, 3, undefined, undefined, undefined]
arr.length = 2;
arr; // arr变为[1, 2]
```
其他多数语言在访问索引超过数组长度会出现越界错误，JavaScript则不会：
```javascript
var arr = [1, 2, 3];
arr[5] = 'x';
arr; // arr变为[1, 2, 3, undefined, undefined, 'x']
```
数组也有很多的方法，感觉`splice()`这个方法比较叼，用起来比较复杂，它可以从指定位置删除指定个数的元素，然后再从该位置增加指定的元素：
```javascript
var arr = ['Microsoft', 'Apple', 'Yahoo', 'AOL', 'Excite', 'Oracle'];
// 从索引2开始删除3个元素,然后再添加两个元素:
arr.splice(2, 3, 'Google', 'Facebook'); // 返回删除的元素 ['Yahoo', 'AOL', 'Excite']
arr; // ['Microsoft', 'Apple', 'Google', 'Facebook', 'Oracle']
// 只删除,不添加:
arr.splice(2, 2); // ['Google', 'Facebook']
arr; // ['Microsoft', 'Apple', 'Oracle']
// 只添加,不删除:
arr.splice(2, 0, 'Google', 'Facebook'); // 返回[],因为没有删除任何元素
arr; // ['Microsoft', 'Apple', 'Google', 'Facebook', 'Oracle']
```
join()方法也比较实用，它可以将数组元素使用指定的连接符连接为新的字符串
```javascript
var s=['arr','p','q'];
var x=s.join('-');
alert(x)//输出为arr-p-q
```

slice()的用法和substring()用法相似：
```javascript
var arr = ['A', 'B', 'C', 'D', 'E', 'F', 'G'];
arr.slice(0, 3); // 从索引0开始，到索引3结束，但不包括索引3: ['A', 'B', 'C']
arr.slice(3); // 从索引3开始到结束: ['D', 'E', 'F', 'G']
```

###对象
对象的形式：
```javascript
var xiaoming={
    name:"xiaoming",
    age:16,
    'middle-school':'No1 Middle School'
}
```
需要注意的有以下几点：
1. 对象的属性之间要用逗号隔开，但是最后一个（末尾）不用加逗号，因为有的浏览器会报错
2. 属性名最好按照变量名的规范来，如果属性名含有某些特殊符号，则需要使用`''`来表示，访问属性值时，形式为：`xiaoming.name`,`xiaoming.age`,但是像'middle-school'这种含有特殊符号的属性值的访问，就必须要使用如下方式：`xiaoming['middle-school']`,当然之前的标准变量名的属性值也可以使用这样的方式访问：`xiaoming['name']`。
3. 访问对象的属性值时，如果没有该属性，则javascript不会报错，而是返回`undefined`的类型
4. 对象内的属性可以动态的删除和增加
```javascript
var xiaoming = {
    name: '小明'
};
xiaoming.age; // undefined
xiaoming.height = 1.70; // 新增一个age属性
xiaoming.height; // 1.70
delete xiaoming.age; // 删除age属性
xiaoming.age; // undefined
delete xiaoming['name']; // 删除name属性
xiaoming.name; // undefined
delete xiaoming.school; // 删除一个不存在的school属性也不会报错
```

5. 判断一个对象是否包含某一属性：使用`hasOwnProperty()`可以判断该属性是否是该对象自身拥有的（不包括继承而来的属性），`in`则包含继承而来的属性。
```javascript
'toString' in xiaoming; // true
xiaoming.hasOwnProperty('name'); // true
xiaoming.hasOwnProperty('toString'); // false
```
6. Array也是对象，而它的每个元素的索引被视为对象的属性

使用for循环取出对象中的属性的方法：
```javascript
var o = {
    name: 'Jack',
    age: 20,
    city: 'Beijing'
};
for (var key in o) {
    alert(key); // 'name', 'age', 'city'
}
```

###Map和Set
javasript的对象使用`{}`来表示，其中的键必须是字符串。但是使用数字或其他类型当键也是很合理的，为了解决这个问题，ES6规范提出了Map和Set。
####Map
查找速度很快，形式如下：
```javascript
var m = new Map([['Michael', 95], ['Bob', 75], ['Tracy', 85]]);
m.get('Michael'); // 95
var m = new Map();
m.set('Adam', 67);
m.set('Adam', 88);
m.get('Adam'); // 88
```

####Set
Set的初始化，需要一个Array，或者直接创建空的Set然后赋值：
```javascript
var s1 = new Set(); // 空Set
var s2 = new Set([1, 2, 3]); // 含1, 2, 3
```

需要注意的是，Set中不包含重复元素，虽然可以向Set中添加重复的元素，但是Set中不会显示重复的元素:
```javascript
var s=new Set([1,2,3,3,"3"]);//s为{1,2,3,"3"}
var s2 = new Set([1, 2, 3]); // 含1, 2, 3
{1, 2, 3, 4}
>>> s.add(4)
>>> s
{1, 2, 3, 4}
```

####iterable

为了统一集合类型，ES6标准引入了新的iterable类型，Array、Map和Set都属于iterable类型。直接使用iterable内置的forEach方法，它接收一个函数，每次迭代就自动回调该回调函数。
```javascript
var m = new Map([[1, 'x'], [2, 'y'], [3, 'z']]);
m.forEach(function (value, key, map) {
    alert(value);
});
```

##函数

###函数定义
与其他语言java，c++相似，需要注意的几点：

+ 函数如果没有`return`语句，函数也会有返回值，返回的类型为`undefined`。
+ javascript中函数也是一个对象，而函数名可以看做指向该函数的变量。故函数的定义还可以使用以下方式：
```javascript
var abs = function (x) {
    if (x >= 0) {
        return x;
    } else {
        return -x;
    }
};
```
**NOTE:**使用这种方式，需要在末尾加`;`，表示赋值语句结束
+ javascript允许向函数中传入任意个数的参数，如果参数个数多于函数的参数，则多余的参数在函数内部不会被使用，并不会报错；若传入的参数个数少于函数参数，则缺少的参数在函数内部被赋值为`undefined`。要防止收到`undefined`,可以在函数内部增加判断：
```javascript
function abs(x){
    if(typeof x!='number'){
        throw 'Not a number!';
    }
    if(x>=0){
        return x;
    }
    else{
        return -x;
    }
}
```

+ argument参数，可以直接在函数内部使用，它指向传入函数的所有参数。
```javascript
function foo(x) {
    alert(x); // 10
    for (var i=0; i<arguments.length; i++) {
        alert(arguments[i]); // 10, 20, 30
    }
}
foo(10, 20, 30);
```

+ rest参数，接收多余的参数，以数组形式保存在rest中。rest参数只能写在最后，前面用`...`标识.
```javascript
function foo(a, b, ...rest) {
    console.log('a = ' + a);
    console.log('b = ' + b);
    console.log(rest);
}

foo(1, 2, 3, 4, 5);
// 结果:
// a = 1
// b = 2
// Array [ 3, 4, 5 ]

foo(1);
// 结果:
// a = 1
// b = undefined
// Array []
```

###generator生成器使用
generator看上去像是一个函数，可以多次返回函数值。它的定义方式如下：
```javascript
function* fib(max)
{
    var t,a=0,b=1,n=1;
    while(n<max){
        yield a;
        t=a+b;
        a=b;
        b=t;
        n++;
    }
    return a;
}
```
上述函数是一个生成斐波那契数列的函数。generator的定义是由:`function*`来定义的，使用`yield`来返回函数值，这时函数并不会终止。直接调用的的话不可以，必须使用如下两种方式调用：
1. 不断的调用`next()`方法
```javascripit
var f = fib(5);  //仅仅是创建了generator对象
f.next(); // {value: 0, done: false}
f.next(); // {value: 1, done: false}
f.next(); // {value: 1, done: false}
f.next(); // {value: 2, done: false}
f.next(); // {value: 3, done: true}
```
2. 使用`for...of`循环迭代来调用
```javascript
for (var x of fib(5)) {
    console.log(x); // 依次输出0, 1, 1, 2, 3
}
```

##对象
可以使用typeof来查看对象类型。在javascript中，一切皆为对象。
```javascript
typeof 123; // 'number'
typeof NaN; // 'number'
typeof 'str'; // 'string'
typeof true; // 'boolean'
typeof undefined; // 'undefined'
typeof Math.abs; // 'function'
typeof null; // 'object'
typeof []; // 'object'
typeof {}; // 'object'
```
但是`null,[],{}`使用`typeof`区分不出来，解决的办法：
1. 判断`Array`要使用`Array.isArray(arr)`来判断
2. 判断`null`要使用`myVar===null`来判断
其中javascript中还有包装对象，新建包装对象要使用`new`。但是包装对象的类型均为`object`
```javascript
var n = new Number(123); // 123,生成了新的包装类型
var b = new Boolean(true); // true,生成了新的包装类型
var s = new String('str'); // 'str',生成了新的包装类型

typeof new Number(123); // 'object'
new Number(123) === 123; // false

typeof new Boolean(true); // 'object'
new Boolean(true) === true; // false

typeof new String('str'); // 'object'
new String('str') === 'str'; // false
```
如果不写关键字`new`，则不是新建包装对象，而是将它们当作函数使用，将变量转变为相应类型的变量。
```javascript
var n = Number('123'); // 123，相当于parseInt()或parseFloat()
typeof n; // 'number'

var b = Boolean('true'); // true
typeof b; // 'boolean'

var b2 = Boolean('false'); // true! 'false'字符串转换结果为true！因为它是非空字符串！
var b3 = Boolean(''); // false

var s = String(123.45); // '123.45'
typeof s; // 'string'
```

* 判断某个全局变量是否存在，使用的方法：`typeof window.myVar==='undefined'`
* 函数内部判断某个变量是否存在：`typeof myVar==='undefined'`
**数字转string的方式**:
```javascript
123..toString(); // '123', 注意是两个点！
(123).toString(); // '123'
```
使用`123.toString()`是会报错的。