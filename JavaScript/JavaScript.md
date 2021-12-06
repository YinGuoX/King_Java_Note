# JavaScript

# 1. 什么是JavaScript

- 一门世界上最流行的脚本语言
- 历史：百度去
- ECMAScript：JavaScript的一个标准，现在已经到es6，但是大部分浏览器好只停留支持es5代码上，因此
  - 导致开发环境--线上环境版本不一致

## 2. 快速入门

### 2.1 引入JavaScript

- 内部标签引入
- 外部引入

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>

<!--    script标签内，写javascript代码-->
<!--    <script>-->
<!--        alert("hell,world!");-->
<!--    </script>-->
<!--    外部文件引入js-->
<!--    不用显示定义type，默认也是javasript-->
    <script src="js/test1.js" type="text/javascript">

    </script>
</head>
<body>

</body>
</html>
```

### 2.2 基本语法入门

- 使用浏览器进行调试：Elements、Console、Sources、Network、Application

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script>
        // 1.定义变量： 变量类型 变量名 = 变量值;
        var num = 1;
        alert(num);
        // 2.条件控制
        if(num>-1){
            alert("true");
        }

        // console.log(num) 在浏览器的控制台打印变量！ 相当于java的sout

    </script>
</head>
<body>

</body>
</html>
```

### 2.3 数据类型

- 变量：var

- 数字、文本、图形、音频、视频...

- number：js不区分小数和整数

- 字符串：’abc‘,"abc"

- 布尔值：true、false

- 逻辑运算：&& || !

- 比较运算：

  - == ：类型不一样但是值一样也会为true; 如：1=='1'
  - === ：绝对等于：类型一样值一样才会为true

- 须知：

  - Nan==Nan：这个与所有的数值都不相等，包括自己
    - 只能通过isNan()：判定是否是为Nan
  - 浮点数问题
    - 1/3==1-2/3：false
    - 避免使用浮点数进行运算，存在精度问题
      - 可以使用阈值比较来代替==

- null：空

- undefined：未定义

- 数组：使用中括号[..]定义

  - java中的数组必须是要相同类型的对象，js无需这样

  - ```html
    // 保证代码的可读性，尽量使用[]
    var arr = [1,3,6,null,true];
    
    new Array(1,13,46,'hello',null);
    ```

  - 取下标数组如果越界了就会报undefine，而不是报错

- 对象：使用大括号{...}定义

  - 每个属性之间使用逗号隔开，最后一个不需要添加

  - ```html
    // Java：Person person = new Person(1,2,3,4,5);
    var person={
    	name:"xxx",
    	age:3,
    	tags:['xs','xsa']
    }
    // 取对象的值
    person.name
    person.age
    ```

### 2.4 严格检查格式

- 'use strict';严格检查模式，预防javaScript的随意性导致产生的一些问题，必须在javascript的第一行

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
<!--
    此时IDEA 需要设置支持ES6语法
    'use strict';严格检查模式，预防javaScript的随意性导致产生的一些问题，必须在javascript的第一行
    局部变量建议使用let去定义=es6 的let
-->
    <script>
        'use strict';
        // 此时是全局变量
        // i=1;
        var x = 1;
        let s  = 1;
        let b =3;
    </script>
</head>
<body>

</body>
</html>
```

## 3. 数据类型

### 3.1 字符串

-  正常字符串使用单引号或者双引号
- 注意转义字符

```javascript
\'
\n
\t
\u4e2d \u#### Unicode字符
\x41 		  AcsII字符
```

- 多行字符串编写

```javascript
// tab上面的esc键下面
var msg = `
jjj
xxx
ccc`;
```

- 模板字符串

```javascript
let a = "xx";
let b = 100;
let msg = `aaa,${a},${b}`; // 不是''是``
```

- 字符串的各种方法和属性：和java中的String类似
- 字符串不可变！不可赋值

### 3.2 数组

- Array可以包含任意的数据类型
- 长度：arr.length
  - 给长度赋值，数组大小会发生变化，变大填充undefine，empty，变小直接裁剪
- 方法和属性和java类似
- slice()：截取Array的一部分，返回一个新数组，类似于String中的substring()
- push()、pop()：尾部操作
- unshift()、shift()：头部操作
- concat()：连接，返回新的数组
- join()：使用特定的字符串拼接
- 多维数组

```javascript
arr = [[11,1],['x','a']]
```

### 3.3 对象

- 由若干个键值对组成，{....}表示一个对象
- 所有键都是字符串，值是任意对象

```javascript
var 对象名 = {
    属性名: 属性值,
    属性名: 属性值,
    ...
    属性名: 属性值
}
```

- 使用一个不存在的对象属性，不会报错！ undefined
- 动态删减属性：通过delete删除对象的属性
- 动态的添加：直接给新的属性添加值即可

```javascript
delete person.name;
person.xx = 'xx';
```

- 判定一个属性值是否在这个对象中,xxx in xxx!

```javascript
'age' in person
>true
// 继承
'toString' in person
>true
```

- 判定一个属性是否是这个对象自身拥有，hasOwnProperty()

```javascript
person.hasOuwProperty('toString');
```

### 3.4 流程控制

- if判断：和java一样
- while、for循环：和java一样
- for(var num in age){...}：num是age的索引

### 3.5 Map、Set

- ES6新提出的
- 和java中的Map、Set类似

```javascript
        var map = new Map([['x',1],['v',2],['as','sa']]);
        var name = map.get('as');
        map.set("as",12);
        map.set('jj',12);
        map.set(6,10);
		map.delete('x');
        console.log(name);
        
        // 去重
        var set = new Set([1,1,1,2,33,2]);
```

### 3.6 iterator

```javascript
// 通过for of 获取值，for in 是下标
// 可以遍历arr,map,set
for(var x of arr){
    console.log(x);
}
```

## 4. 函数

方法：对象里面的

函数：对象外面的

### 4.1 定义函数

```javascript
// 定义方式1
function abs(x){
    if(x>=0){
        return x;
    }else{
        return -x;
    }
}
```

- 如果没有执行return,函数执行完也会返回结果，返回Nan(因为undefined)

```javascript
// 定义方式2
var abs = function(x){
    ...
}
```

- 参数问题：
  - 少和多了都可以接收，返回结果
- 如果不存在参数，如何规避？

```javascript
var abs = function(x){
    // 手动抛出异常来规避
    if(typeof x!=='number'){
        throw "Not a number";
    }
    if(x>=0){
        return x;
    }else{
        return -x;
    }
}

```

- 如果存在多个参数
  - arguments是js免费赠送的关键字
  - 代表传递进来的所有参数，是一个数组

```javascript
var abs = function(x){
    for(var i = 0;i<arguments.length;i++){
        console.log(arguments[i]);
    }
    if(x>=0){
        return x;
    }else{
        return -x;
    }
}
```

- 问题：arguments包含所有的参数，有时候想使用多余的参数来进行附加操作，则需要排除已有的参数，使用arguments会很麻烦
  - 可以使用rest，ES6新特性
  - 获取除了已经定义好了的参数之外的参数
  - 必须写在最后面，用...来标识

```javascript
function aaa(a,b,...rest){
    console.log('a='+a);
}
```

### 4.2 变量的作用域

- javascript中，var定义变量是存在作用域的
- 如果变量在函数体内声明，函数体外使用，则会报错，如果真的想用，可以研究一下闭包！
- javascript中函数查找变量是从自身开始，由内向外开始查找，如果内部有，则会屏蔽外部的函数变量
- 提升变量的作用域

```javascript
function xx(){
	var x = 'x' + y;
	console.log(x);
	var y = 'y';
}
> xunderfined
```

- 说明js执行引擎自动提升了y的声明，但是不会提升y的赋值
- 因此养成规范：所有的变量定义都放在函数的头部，不要乱放，便于代码维护。
- 全局对象window：
  - 默认所有的全局变量都会自动绑定在window对象上
  - alter()本身也是window对象上
- javasctript实际上只有一个全局作用域，任何变量(函数)如果没有找到想要的变量，都会向外查找，如果在window下都没有找到，就会报错RefrenceError

> 规范：
>
> - 由于所有的全局变量都会绑定到window上，如果不同的js文件使用了相同的全局变量，则会产生冲突
> - 如何解决？

- 把自己的代码全部绑定到自己定义的唯一空间名字中,降低全局命名冲突的问题！

```javascript
// 唯一全局变量
var King = {};

// 定义全局变量
King.name = "xxx";
King.add = function(x,y){
    return x+y;
}
```

> 局部作用域 let
>
> - ES6 新提出的
> - 解决局部作用域冲突的问题

```javascript
function add(){
    for(var i = 0;i<10;i++){
        console.log(i);
    }
    console.log(i);//因为是var i没有消失，还可以使用，因此最好使用let
}
```

> 常量 const
>
> - ES6 新提出的
> - 在ES6之前，定义常量：用全部大写的变量名标识，建议不要改，但是可以改

### 4.3 方法

- 定义方法：
  - 就是把函数放在对象的里面，对象只有两个对象：属性和方法

```javascript
var king={
    name:'ds',
    birth:1222,
    age:function(){
        var now = new Date().getFullYear();
        return now - this.birth;
    }
}
// 调用
king.name
king.age()

// 等同于
getAge = function(){
        var now = new Date().getFullYear();
        return now - this.birth;
    }
var king={
    name:'ds',
    birth:1222,
    age:getAge
}
// 调用
king.name
king.age()
```

- this.代表什么？
  - 代表当前调用这个this的对象
  - 所以单独使用getage()会返回NaN

> apply
>
> - 在js中可以控制this的指向

```javascript
// 等同于
getAge = function(){
        var now = new Date().getFullYear();
        return now - this.birth;
    }
var king={
    name:'ds',
    birth:1222,
    age:getAge
}
getAge.apply(king,[]);// this指向king,[]参数为空
```

## 5. 内部对象

> 标准对象
>
> - 可以使用typeof 变量 查看对象的类型

```javascript
typeof set
"object"
typeof 'x'
"string"
typeof 1
"number"
typeof async
"undefined"
typeof asa
"undefined"
typeof map
"object"
```

### 5.1 Date

- 基本使用和转换

```javascript
var now = new Date();
now.getFullYear();
now.getMonth(); //0-11月
now.getDate(); // 日
now.gerDay();//星期几
now.getHours();//时
now.getMinutes();
now.getSeconds();
now.getTime();//时间戳，全世界统一 1970 1.1 0:00:00 毫秒数

// 时间戳转换为时间
new Date(时间戳);

// 获得本地时间的字符串
now.toLocaleString();

// 其他时区的时间字符串
now.toGMTString();
```

### 5.2 JSON

> JSON：
>
> - 轻量级的数据交换格式，可以提升网络数据传输效率
> - 在JavaScript中一切皆对象，任何js支持的类型都可以用JSON来表示
> - 格式：
>   - 对象都用{}
>   - 数组都用[]
>   - 所有的键值对都是用key:value

- JSON字符串和JS对象的转换

```javascript
var user = {
    name:"xx",
    age:3,
    sex:'x'
}

// 对象转换为json字符串:"{\"name\":\"xx\",\"age\":3,\"sex\":\"x\"}"
var jsonUser = JSON.stringify(user);

// json字符串转换为对象:{name: "xx", age: 3, sex: "x"}
var obj = JSON.parse("{\"name\":\"xx\",\"age\":3,\"sex\":\"x\"}")
```

## 6. 面向对象编程

- java、javascript、c#，，，都是面向对象的，但是javascript有一些不同
  - 类：模板
  - 对象：具体的实例
- 在javaScript中需要转换一下思维方式，因为把类与对象用到了极致
  - 原型：类似继承，但是可以随意指向

```javascript
var Student={
    name:'xx',
    age:3,
    run:function(){
        console.log(this.name+'run,,,')
    }
};

var xiaoming = {
    name:'xiaoming'
};
// 指向一个原型对象，父类
xiaoming.__proto__ = Student;
var Bird={
    run:function(){
        console.log(this.name+'fly,,,')
    }
};
xiaoming.__proto__ = Bird;
```

> class 继承
>
> - 在ES6中引入的

```javascript
// 原生的
function Student(name){
    this.name = name;
}
// 给Student添加一个方法
Student.prototype.hello = function(){
    alert("hello");
};

// ES6之后======================
// 定义一个类
class Student{
    // 定义构造器
    constructor(name){
        this.name = name;
    }
    hello(){
        alert("hello");
    }
}

// 使用
xiaoming = new Student("xiaoming");
xiaoming.hello()
```

- 继承

```javascript
//功能类似xiaoming.__proto__ = Student;
class XiaoStudent extends Student{
    constructor(name,grade){
        super(name);
        this.grade =grade; 
    }
    myhello(){
        alert("hello");
    }
}
```

- 本质：查看对象原型就知道了

> 原型链：
>
> - --proto--：百度！



## 7. 操作BOM对象

### 7.1 浏览器介绍

- JavaScript和浏览器的关系？
  - JavaScript诞生就是为了能够在浏览器中运行
- BOM：浏览器对象模型 Brower Object Model
  - IE 6-11
  - Chrome
  - Safari
  - FireFox
  - Opera
- 第三方浏览器
  - QQ浏览器、360浏览器

### 7.2 window

- window代表浏览器窗口

```javascript
window.innerHeight
1147
window.innerWidth
225
window.outerHeighth
undefined
window.outerHeight
1038
window.outerHeight
1038
window.innerWidth
475
```

> Navigator
>
> - 封装了浏览器的信息
> - 大多数时候不会使用navigator对象，因为大多数时候会被人为修改
> - 因此不建议使用这些属性来判断和编写代码

```javascript
window.navigator
Navigator {vendorSub: "", productSub: "20030107", vendor: "Google Inc.", maxTouchPoints: 0, userActivation: UserActivation, …}
window.navigator.appName
"Netscape"
window.navigator.appVersion
"5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/92.0.4515.159 Safari/537.36"
window.navigator.userAgent
"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/92.0.4515.159 Safari/537.36"
window.navigator.platform
"Win32"
```

> screen
>
> - 代表屏幕信息

```javascript
screen.width
1920
screen.height
1080
```

> location
>
> - 代表当前页面的URL信息

```javascript
location
Location {ancestorOrigins: DOMStringList, href: "https://www.baidu.com/", origin: "https://www.baidu.com", protocol: "https:", host: "www.baidu.com", …}
ancestorOrigins: DOMStringList {length: 0}
assign: ƒ assign() 
hash: ""
host: "www.baidu.com" // 主机
hostname: "www.baidu.com"
href: "https://www.baidu.com/" // 链接
origin: "https://www.baidu.com" 
pathname: "/"
port: ""
protocol: "https:" // 协议
reload: ƒ reload() // 重新加载（刷新网页）
replace: ƒ replace()
search: ""
toString: ƒ toString()
valueOf: ƒ valueOf()
Symbol(Symbol.toPrimitive): undefined

// 设置新的网址
location.assign("https://sxxx/xx")
```

> document
>
> - 代表当前页面，HTML DOM 文档树
> - 可以获取具体的文档树节点
> - 可以获取浏览器的cookie

```javascript
document.title
"百度一下，你就知道"
document.title='ws'
"ws"
```

```javascript
<dl id="app">
    <dt>1</dt>
	<dt>2</dt>
	<dt>3</dt>
</dl>

<script>
	var dl = document.getElementById('app');    
</script>
```

```javascript
# 获取cookie
document.cookie
"BAIDUID=2A2D8B9703E93FC3AD27914ABB464732:FG=1; BAIDUID_BFESS=2A2D8B9703E93FC3AD27914ABB464732:FG=1; BIDUPSID=2A2D8B9703E93FC3AD27914ABB464732; PSTM=1614610136; BD_HOME=1; H_PS_PSSID=34433_31253_34402_33848_34092_34106_26350_34420_22158_34390; ai_user=mPewvoAM/5IvcOWMy015BE|2021-08-22T08:24:57.407Z; BD_UPN=12314753; BA_HECTOR=8l2g240l848ka004ei1gi42hd0q"
# 劫持cookie原理
<script src='aa.js'></script>
# 恶意人员：获取你的cookie上传到他的服务器
# 服务器端可以设置cookie：httpOnly来防止cookie被劫持
```

> history
>
> - 代表浏览器的历史记录

```javascript
history.back();//后退
history.forward();//前进
```

## 8. 操作DOM对象

- DOM：文档对象模型
- 浏览器网页就是一个DOM树型结构
  - 更新：更新一个DOM节点
  - 遍历DOM节点：得到DOM节点
  - 删除：删除一个DOM节点
  - 添加：添加一个DOM节点
- 要操作一个DOM节点，就必须要先获得这个DOM节点

> 获取DOM节点
>
> - 原生操作

```html
<body>
<div id="father">
    <h1>标题1</h1>
    <p id="p1">p1</p>
    <p class="p2">p2</p>

</div>
<script>
    var h1 = document.getElementsByTagName('h1');
    console.log(h1);
    var p1 = document.getElementById('p1');
    console.log(p1);
    var p2 = document.getElementsByClassName('p2');
    console.log(p2);

    var father = document.getElementById('father');

    // 获取父节点下面的所有子节点
    var childerns = father.children;
    // father.firstChild;
    // father.lastChild;
</script>
</body>
```

> 更新DOM节点

```html
<div id='id1'>
    
</div>
<script>
	var id1 = document.getElementById('id1')
    // 操作文本
    id1.innerText='456';// 修改文本的值
    id1.innerHTML = '<strong>123</strong>';//可以解析HTML标签
    
    // 操作CSS
    id1.style.color='yellow';//属性使用字符串包裹
    id1.style.fontSize='20px';//驼峰命名
    id1.style.padding='2em';
</script>
```

> 删除DOM节点
>
> - 先获取父节点，在通过父节点删除自己

```html
<body>
<div id="father">
    <h1>标题1</h1>
    <p id="p1">p1</p>
    <p class="p2">p2</p>

</div>
<script>
    var h1 = document.getElementsByTagName('h1');
    console.log(h1);
    var p1 = document.getElementById('p1');
    console.log(p1);
    var p2 = document.getElementsByClassName('p2');
    console.log(p2);

    var father = p1.parentElement;
	// 删除节点，方法一
	father.removeChild(p1);
    // 删除是一个动态的过程
    father.removeChild(father.children[0])
</script>
</body>
```

> 插入节点
>
> - 获得了某个DOM节点
> - 假设这个DOM节点是空的，可以通过innerHTML就可以增加一个元素，
> - 但是如果DOM节点不为空，则不能执行以上操作，因为会产生覆盖
> - 此时应该用追加append()

```html
<p id='js'>
    javaSctipy
</p>
<div id="list">
    <p id='se'>
        javase
    </p>
    <p id ="ee">
         javaee  
    </p>
</div>

<script>
	var js = document.getElementById('js');
    var list = document.getElementById('list');
	// 会把js这个节点直接移到list里面
    list.appendChild(js);
    // 创建一个新的节点
    var newP = document.createElement('p');// 创建一个p标签
    newP.id = 'newP';
    newP.innerText = 'hahhaha';
    list.appendChild(newP);
    // 创建一个新的标签节点
    var myScript = document.createElement('script');
    // 使用这种方式可以设置任意的属性和值
    myScript.setAttribute('type','text/javascript');
    list.appendChild(myScript);
    
    // 创建一个Style标签
    var myStyle = document.createElement("style");
    myStyle.setAttribute('type','text/css');
    myStyle.innerHTML = 'body{backgroud-color: red;}';//设置标签内容
    // 插入到head中生效
    document.getElementsByTagName('head')[0].appendChild(myStyle);
    
    
    //插入到节点之前
    var js = document.getElementById('js');
    var ee = document.getElementById('ee');
    var list = document.getElementById('list');
    // 将js节点插入到ee节点前面
    list.insertBefore(js,ee);
</script>
```

## 9. 操作表单

- 表单是什么？form：也是DOM树的一个节点
  - 文本框 text
  - 下拉框 select
  - 单选框 radio
  - 多选框 checkbox
  - 隐藏域 hidden
  - .... 
- 表单的目的：提交信息
- 获得表单的信息

```html
<form action="post">
    <span>用户名：</span> <input type="text" id="username">

<!--    单选框的值就是定义好了的value-->
    <p>
        <span>性别：</span>
        <input type="radio" name = 'sex' value="man" id="boy">男
        <input type="radio" name = 'sex' value="woman">女
    </p>
</form>
<script>
    var input_text = document.getElementById('username');
    //得到输入框的值
    input_text.value;
    // 修改输入框的值
    input_text.value='123';
    // 对于单选框、多选框等固定的值，boy.value只能获取当前的值，不能获取选中的值
    var boy = document.getElementById('boy');
    // 要获取选中的值需要使用checked进行if判断来获取
    // if(boy.checked){
    //
    // }
    // 赋值
    boy.checked=false;
</script>
```

- 过滤、验证表单

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="https://cdn.bootcss.com/blueimp-md5/2.10.0/js/md5.min.js"></script>
</head>
<body>
<!--表单绑定提交事件
onsubmit=绑定一个提交检测的函数 true,flase
将结果返回给表单，使用onsubmit接收
-->
<form action="https://www.baidu.com/" method="post" onsubmit="return aaa()">
    <p>
        <span>用户名：</span> <input type="text" name = "username" id="username">
    </p>
    <p>
        <span>密码：</span> <input type="password" id="input-password">
    </p>
    <input type="hidden" id="md5-password" name="password">
<!--    绑定事件 onclick 被点击-->
    <button type="submit" >提交</button>
</form>
<script>
    function aaa(){
        var uname = document.getElementById('username');
        var pwd = document.getElementById('input-password');
        var md5pwd = document.getElementById('md5-password');
        md5pwd.value=md5(pwd.value);
        // 可以校验表单内容，true就是通过提交，false就是组织提交
        return true;
    }

</script>
</body>
</html>
```

## 10. jQuery

- jQuery库：里面存在了大量的JavaScript函数

> 获取jQuery
>
> - 可以在官网下载
> - 也可以使用cdn 在线引入，搜索cdn jquery即可了解

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="http://libs.baidu.com/jquery/2.0.0/jquery.min.js"></script>
<!--    <script src="lib/jquery-3.6.0.js"></script>-->
</head>
<body>
<!--
公式：$(selector).action()!!!
-->
<a href="" id="test-jquery">点我</a>
<script>
    // 原生
    // var x = document.getElementById('test-jquery');
    // x.click(function (){
    //    alert('hello');
    // });

    // 选择器就是css选择器
    $('#test-jquery').click(function (){
        alert('hello');
    });

</script>
</body>
</html>
```

> 选择器
>
> - 不懂的可以直接查jQuery文档工具栈！！！
> - https://jquery.cuishifeng.cn/

```html
<script>
    // 原生js
    // 标签
    document.getElementsByTagName();
    // id
    document.getElementById();
    // 类
    document.getElementsByClassName();


    // jQuery css中的选择器都它都可以使用!=>
    $('p').click();// 标签选择器
    $('#idname').click();// id选择器
    $('.classname').click();// 类选择器

</script>
```

> 事件
>
> - 鼠标事件
> - 键盘事件
> - 其他事件
> - 查文档即可

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="lib/jquery-3.6.0.js"></script>
    <style>
        #divMove{
            width:500px;
            height:500px;
            border:1px solid red;
        }
    </style>
</head>
<body>
<!--要求：获取鼠标当前的一个坐标-->
mouse:<span id = "mouseMove"></span>
<div id="divMove">
    在这里移动鼠标试试？？？
</div>
<script>
    // 当网页元素加载完毕后，响应事件
    // $(document).ready(function (){
    //
    // });
    // 简化
    $(function (){
        $('#divMove').mousemove(function (e){
            $('#mouseMove').text("x:"+e.pageX+' y:'+e.pageY);
        })
    })
</script>

</body>
</html>
```

> 操作DOM
>
> - 查jquery文档才是永远的神！！！

```html
<ul id="test-ul">
    <li class="js">javascript</li>
    <li class="python">python</li>
</ul>
<div id="divMove">

</div>
<script>
    // 操作文本
    $('#test-ul li[class=python]').text();//获取值
    $('#test-ul li[class=python]').text('xxx');//设置值，类似重载
    // 操作css
    $('#test-ul li[class=python]').css({"color":"red"})
    // 显示和隐藏
    $('#test-ul li[class=python]').show()
    // 本质是display:none
    $('#test-ul li[class=python]').hide()

</script>
```

# 总结

- 看jQuery源码、看游戏源码：开源之家自己搜
- 扒网页！！！
- layer弹窗主键
- Element-ui