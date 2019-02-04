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

### 对象枚举
例如有一个对象obj是这样子的
```json
{
    "a":"aaa",
    "b":"bbb",
    "c":"ccc"
}
```
如果调用Object.key(obj),将会返回["a","b","c"]。
如果调用Object.value(obj),将会返回["aaa","bbb","ccc"]

好处就是可以不用写for循环了。
其实真正的好事是可以简单的进行链式调用。
例如对象person是这个样子的
```json
{
    "alan":{"ishere":ture,"seat":"1A"},
    "bob":{"ishere":ture,"seat":"2C"},
    "may":{"ishere":ture,"seat":"10F"},
    "jacky":{"ishere":false,"seat":"2A"},
    "peter":{"ishere":ture,"seat":"3D"}
}
```
我们就可以链式调用的渲染出内容
```js
Object.value(person).filter((key)=>key.ishere).map((key)=><div>{key.seat}</div>)
```
虽然es5也能做链式调用，不过结果颇为冗长，不可能达到如此简洁的效果。
此外再说一点，不提倡再es6中使用冗长的for循环。需要用for循环进行处理的可以通过map函数+filter函数进行处理。

此外对象还有其他方法，不过使用的情况较少，这里就不多说了。
## 数组新增方法
### 包含
```js
["1","bob",true].include("1") //返回true
```
很简单是不是，在es5中需要用indexof去处理，比较麻烦。
此外顺带提一句，以下的代码可以用Array.include去处理
```js
if (obj.canbook == "1" || obj.canbook == "6"){
}
```
其实这样的代码有待商榷。如果canbook的情况增加到1、3、6、10的时候难道我们还要继续这样或下去吗？
```js
const canbookList = ["1","6"]
if (canbookList.include(obj.canbook)){
}
```

### 解构
解构和对象的解构一样
```js
const a =[111,22,33,444]
Math.max(...a)//返回数组的最大值
[foo,...bar]=a//foo=111,bar=[22,33,444]
```
同时，它还有个逆运算Array.of
```js
Array.of(111,22,33,444)//[111,22,33,444]
```
此外数组还有转换等方法，不过这个我们留待后面再讲。