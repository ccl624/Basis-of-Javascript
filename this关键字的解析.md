# JavaScript中this关键字的解析
   我们需要根据 "调用位置" 上函数的 "调用方式" 来确定函数中this使用的   "绑定规则"
## 1. 确定调用位置
```
function baz() {
	//调用栈是baz
	// 因此，当前调用位置是全局作用域
	console.log( "baz");
	bar(); // <-- bar的调用位置
}
function bar() {
	//调用栈是baz-->bar
	// 因此，当前调用位置是在baz中
	console.log( "bar");
	foo(); // <-- foo的调用位置
}
function foo() {
	debugger
	//调用栈是baz-->bar-->foo
	// 因此，当前调用位置是在bar中
	
	console.log( "foo");
}
baz(); // <-- baz的调用位置
```

## 2. 绑定规则
### 1. 默认绑定
1. 独立函数调用时，如果使用了非严格模式，this会绑定到全局对象（window）

```
function foo() {
	console.log( this.a );//2
}
var a = 2;
foo();
```
2. 独立函数调用时，如果使用严格模式，this会绑定到undefined

```
function foo() {
	"use strict"
	console.log( this.a );//this为undefined，所以此处会报错
}
var a = 2;
```
3. 虽然this绑定完全取决于调用位置，但只有独立函数foo运行在非严格模式下时，默认绑定才会绑定到全局对象window，比如以下代码：

```
function foo() {
//  "use strict";如果foo运行在严格模式下调用的时候会报错
	console.log( this.a );
}
var a = 2;
(function(){
	"use strict";
	foo(); //此处调用foo函数并不会报错
})();
```
### 2. 隐式绑定
1. 隐式绑定主要作用于对象，当函数作为对象的方法时，函数里的this指向的是调用这个方法函数的对象，**对象属性引用链中只有最顶层或者最后一层会影响调用位置**

```
function foo() {
	console.log( this.a );//2
}
var a = "oops, global"; 
var obj = {
	a: 2,
	foo: foo
};

//对象属性引用链中只有最顶层或者最后一层会影响调用位置
function foo() {
	console.log( this.a );
}
var obj2 = {
	a: 42,
	foo: foo
};
var obj1 = {
	a: 2,
	obj2: obj2
};
obj1.obj2.foo();//输出42
```
2. **隐式丢失**：当把被隐式绑定的函数赋值给一个变量或者作为参数传入到其他独立函数或者内置函数内部时，其内部的this会指向全局变量window或者undefined（取决于是否是严格模式下）

```
//赋值给一个变量，变量作为函数再调用
function foo() {
	console.log( this.a );
}
var a = "oops, global"; 
var obj = {
	a: 2,
	foo: foo
};
var bar = obj.foo;
bar();//此处输出为"oops, global"

//作为参数传入到一个独立函数内部
function foo() {
	console.log( this.a );
}
function doFoo(fn) {
	fn(); 
}

var a = "oops, global"; 
var obj = {
	a: 2,
	foo: foo
};
doFoo( obj.foo );//此处输出为"oops, global"

//作为参数传入到内置函数中
function foo() {
	console.log( this.a );
}

var a = "oops, global"; 
var obj = {
	a: 2,
	foo: foo
};
setTimeout( obj.foo, 1000 ); //此处输出为"oops, global" 
```
### 3. 显式绑定
1. 显式绑定也指call， apply调用，this指向call或者apply指定的对象

```
function foo(){
    console.log(this.a);
}
var a = "test";
var obj = {
    a:2
}
foo.call(obj);//2
```
2. 硬绑定：如果手动将foo.call(obj)添加到任何一个函数中，无论这个函数怎么调用foo函数的this只指向call指定的对象obj（apply同理）

```
function foo() {
	console.log( this.a );
}
var a =1;
var obj = {
	a:2
};
var obj_test = {
	a:"test"
};
var bar = function() {
	console.log( this.a );
	foo.call( obj );
};

bar(); // 1 2
setTimeout( bar, 1000 ); // 1 2
bar.call( obj_test ); //test  2   硬绑定的bar不可能再修改它的this(指的是foo中的this)
```
3. 硬绑定函数: bind()方法创建一个新的函数, 当被调用时，将其this关键字设置为提供的值，在调用新函数时，在任何提供之前提供一个给定的参数序列。

```
this.num = 9; 
var mymodule = {
  	num: 81,
  	getNum: function() { return this.num; }
};
console.log(mymodule.getNum()); // 81
var getNum = mymodule.getNum;
console.log(getNum());// 9, 因为在这个例子中，"this"指向全局对象
// 创建一个'this'绑定到mymodule的函数
var boundGetNum = getNum.bind(mymodule);
console.log(boundGetNum()); // 81
```
4. 解决隐式丢失
```
//普通对象的属性查找 
function foo(a,b) {
	console.log( this.a,a,b );
}
var obj = {
	a:2
};
function bar(){
    foo.call( obj,"a","b"); 
    foo.apply(obj,["a","b"]);
}
var bar1 = bar;
bar1();//2 a b ; 2 a b
```
5. 当call apply bind 传入的对象为null是this为默认绑定，会绑定到全局或者undefined

```
function foo() {
	console.log(this.a);
}
var a = 2;
var obj={
    a:1
}
foo.call(null);//2
foo.call(obj);//1
```

### 4. new绑定
1. 此处重新定义一下构造函数，构造函数只是一些使用new操作符时被调用的函数，使用new操作符调用函数，构造函数内的this会绑定到生成的对象,
```
function foo(a) {
	this.a = a;
}
var bar = new foo(2);
console.log( bar.a ); // 2
/*使用 new 来调用 foo(..) 时，我们会构造一个新对象并把它绑定到 foo(..) 调用中的 this 上。 new 是最
后一种可以影响函数调用时 this 绑定行为的方法，我们称之为 new 绑定。*/
```
### 5. 绑定规则优先级
new绑定->显式绑定->隐式绑定

```
function foo(something) {
	this.a = something;
}
var obj1 = {
	foo: foo
};
var obj2 = {};
obj1.foo( 2 );
console.log( obj1.a ); // 2

obj1.foo.call( obj2, 3 );//显示绑定->隐式绑定
console.log( obj1.a ); // 2
console.log( obj2.a ); // 3

var bar = new obj1.foo( 4 );//new绑定->隐式绑定
console.log( obj1.a ); // 2
console.log( bar.a ); // 4
```








