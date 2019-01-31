# 对象与数组
## 对象新增方法
### 解构赋值1
```js
const {aaa} = bbb
//等价于
const aaa=bbb.aaa
```
再进一步
```js
const {aaaa:{bbbb,cccc}}=ddddd
//等价于
const bbbb=dddd.aaaa.bbbb
const cccc=dddd.aaaa.cccc
//其中aaaa未被定义，因为这样子不会污染变量
```
本质上是用于减少代码量
配合上函数一起使用可以大幅减少代码量
```js
const a=({input:{firstName='zhang'}})=>{
    console.log(firstName)
}
//es5
var a = function a(_ref) {
  var _ref$input$firstName = _ref.input.firstName,
      firstName = _ref$input$firstName === undefined ? 'zhang' : _ref$input$firstName;
  console.log(firstName);
};
```
上述是es6转es5的翻译器翻译后的版本，我们解释一下。
首先我们并未定义输入的值，所以babel就赋予了一个_ref用来占位，用来过渡（如同将ab两个值交换的第三个临时变量c）。另外，也用了一个_ref$input$firstName来判断是否有值传入。
es6一共65个byte，而es5达到同样的效果一共209个byte
### 解构赋值2
```js
const bbb = {x:1,y:2,z:3}
const ccc = {x:4,q:5,p:6}
const aaa = {...bbb,...ccc,ddd:{x:7,p:8}}
```
aaa的最终结果是
```js
{
    'x':7,
    'y':2,
    'z':3,
    'q':5,
    'p':8
}
```
### 浅拷贝深拷贝
```js
Object.assign({},a,b)//等价于{...a,...b}
```
咦，第一个空对象是拿来干嘛用的呢？
对象的拷贝跟众多语言一样，分为深拷贝和浅拷贝，一个是一模一样完全复制一份出来，另一个是指针指向同一个变量。
有大括号的相当于另立门户，所以属于深拷贝。
而没有大括号的，就是浅拷贝（皆拷贝只assign输入的一个个参数中）咯。
```js
const a = {x:1,y:2}
const b = {y:4,c:3}
const c=Object.assign(a,b)
console.log(a.y,c.y)//4,4
c.y=7
console.log(a.y)//7
```
上述的例子已经清晰的看见何谓浅拷贝了。

### 枚举

