## ES6常用内容
#### 1. let关键字
* 1. 作用：用于声明一个变量，与var相同
* 2. 特点：只在块级作用域有效，不能重复声明，不会做预处理，不存在变量提升
```
//与var的比较
function varTest(){
    var a = 1;
    if(true){
        var a = 2;//相同的变量
        console.log(a);//2
    }
    console.log(a);//2
}

function letTest(){
    let a = 1;
    if(true){
        let a = 2;//不同的变量
        console.log(a);//2
    }
    console.log(a);//1
}
```

```
//for循环中块级作用域的体现
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>let关键字</title>
</head>
<body>

<button>测试1</button>
<br>
<button>测试2</button>
<br>
<button>测试3</button>
<br>
<script type="text/javascript">
    let btns = document.getElementsByTagName('button');
    for(let i = 0;i<btns.length; i++){
        btns[i].onclick = function () {
          alert(i);//点击每个button按钮会弹出对应顺序的i；如果换成var，只能弹出最后一个i，值为3
        }
      }
</script>
</body>
</html>
```
#### 2. const关键字
**const** 用来定义一个常量，这个常量不能被修改，其他的特点与let一样，const用来保存不用改变的数据
#### 3. 变量的==解构==赋值
* 变量的解构赋值就是从数组或者对象中提取数值并赋给多个变量
* 对象的解构赋值
    * let {a,b}={a:"zhangsan",b:"lisi"}
* 数组的结构赋值
    * let [a,b]=[1,2,3,"abc"]

```
  let obj = {
    username:"Zhang San",
    age:18,
    gender:"male"
  }

  let {username,age,gender} = obj;//变量名必须与对象的属性名一致
  console.log(username,age,gender);//Zhang San 18 male

  let arr = [2,3,4,"abc"]
  let [e,f,g] = arr;
  console.log(e,f,g);//2 3 4
```
#### 4. 模板字符串
* 用来简化字符串的拼接
* 模板字符串必须用``包含
* 变化的部分用${**拼接内容**}代替

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>04_模板字符串</title>
</head>
<body>
<script type="text/javascript">
let username = "Zhangsan";
let age = 18;
let gender = "male";
let str = `我的名字是${username},我的年龄是${age},性别${gender}`;
console.log(str);
</script>
</body>
</html>
```
#### 5. 简化对象的写法
* 省略同名的属性值
* 省略方法的 function

```
let username = "zhangsan";
let age = 18;
let gender = 'male';
let obj={
    username,
    age,
    gender,
    myDetail(){
        return this.username+','+this.age+','+this.gender
    }
}
console.log(obj);//{username: "zhangsan", age: 18, gender: "male", myDetail: ƒ}
```
#### 6. 箭头函数
* 作用：用来定义匿名函数
* 基本语法
    * 没有参数：() => 函数体
    * 一个参数：形参 => 函数体
    * 多个参数：(形参1,形参2,...) => 函数体
    * 函数体如果为单个语句，可以不用{}，但会直接返回结果，如果用{},需要手动返回
    * 函数体如果有多个语句，必须用{}
* 箭头函数的特点
    * 简洁
    * 箭头函数没有自己的this，其this不是在调用的时候决定的，而是其处在的块作用域决定的
    * 扩展理解：箭头函数的this判断，先看外层是否有函数，如果有外层函数的this就是箭头函数的this，如果没有则this是window

```
let fun = () => console.log("hellow wordQ")
fun();//hellow word!

let fun1 = (x,y)=>x+y;
console.log(fun1(20, 30));//50

let fun2 = (x,y) =>{
console.log("hellow world!");
return x+y;
}
console.log(fun2(20, 30));//hellow word!

let fun3 = () =>{
console.log(this);
};
fun3();//Window

let obj = {
username:"zhangsan",
getM:function(fun){
   return () =>{
    console.log(this);
  };
}
};
obj.getM();//{username: "zhangsan", getM: ƒ}
```

#### 7. 三点运算符（...）
* rest(可变) 参数，用来替代arguments，比arguments灵活，但作为形参时只能作为最后部分形参

```
function fun(...values) {
    console.log(arguments);
    arguments.forEach(function (item, index) {//arguments是伪数组没有forEach方法，此处会报错
        console.log(item, index);
    });
    console.log(values);
    values.forEach(function (item, index) {//valus得到的是一个数组
        console.log(item, index);
    })
}
fun(1,2,3);
```
* 扩展运算符

```
let arr1 = [1,3,5];
//let arr2 = [2,...arr1,6];
arr2.push(...arr1);//[2,1,3,5,6]
```
#### 7. 形参默认值
当不传实参时，默认使用形参里的默认值

```
function Point(x = 0,y = 0){
this.x = x;
this.y = x;
}
let point = new Point();
console.log(point);//Point {x: 0, y: 0}
let point1 = new Point(1,2);
console.log(point1);//Point {x: 1, y: 1}
```
#### 8. Promise对象
* Promise对象代表着将来以后可能会发生的事情（通常是一个异步操作），使用Promise对象可以讲异步操作以同步的形式表现出来，避免了层层嵌套的回调函数
* 使用Promise的基本步骤
    *  创建promise对象
    *  调用promise的then()
    
```
//此处以箭头函数表示函数
let promise = new Promise((resolve, reject) => {
    //初始化promise状态为 pending
  //执行异步操作
  if(异步操作成功) {
    resolve(value);//修改promise的状态为fullfilled
  } else {
    reject(errMsg);//修改promise的状态为rejected
  }
})
promise.then((msg)=>{
    //异步操作成功之后的操作
},(error)=>{
    //异步操作失败之后的操作
})
```
* Promise对象的三个状态
    * pending：初始状态，不是成功或失败状态
    * fullfilled：操作成功的状态
    * rejected：操作失败的状态

```
//使用Promise处理AJAX请求
let getPromise = (url)=>{
let promise = new Promise((resolve,reject)=>{
  let xhr = new XMLHttpRequest();
  xhr.open("get",url);
  xhr.send();
  xhr.onreadystatechange = function () {
    if(this.readyState == 4){
      if(this.status == 200){
        let data = this.responseText;
        resolve(data);
      }else{
        reject("no message");
      }
    }
  }
});
return promise;
};
getPromise("http://localhost:3000/news?id=123123")
.then((msg)=>{
console.log(msg);
let commentUrl = JSON.parse(msg);
return getPromise(`http://localhost:3000${commentUrl.commentsUrl}`);
},(error)=>{
console.log(error);
}).then((msg)=>{
console.log(msg);
},(error)=>{
console.log(error);
})
```

    








