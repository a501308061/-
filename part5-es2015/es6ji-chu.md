# es6的基础1-变量与函数
## 引言
js作为一种10天就被创作出来的语言，其本身并未经过实验室的打磨。所以，积累了许许多多的问题。同时，当年ie作为市场占有率最高的浏览器，它想怎么做，就怎么做，所以js可以成为一种比较“烂”的语言。
在2013年，ECMA公布了js的标准，我们称之为es5，在2015年公布了es6。解决了很多js长期存在的问题，网页前端迎来爆炸式的增长。

## 变量定义
js的变量的是以var来声明的。es6对其作出了补充
一个是const 一个是let。
const从字面上就可以看出，这个是个常量不可修改。
let 那就是变量咯。
这两个主要是解决js蛋疼的作用域问题。
```js
(function() {
    let a=0
    {
        var b=1
        let c=2
    }
    console.log(a,b,c)
})()
```
这段代码会报错，因为c是未定义的。长期以来，js的作用域只能靠立即执行函数进行作用域的限定。因为var这个东西很强大，出了函数，其余的情况下变量都能“穿透”，而其他类C的语言，大括号中定义的变量一旦到了括号外，就会被清除掉。
所以提出了const 和 let 这两个变量声明方式。
一般情况下，在es6的环境中，不建议使用var。

## 箭头函数
如在闭包一节中
```js
var obj = {
  name : "mybook",
  say : function(){
    console.log(this.name);//mybook
  }
}
obj.say();
```
我们采用箭头函数进行改写
```js
var name='mybook2'
var obj = {
    name : "mybook",
    say : ()=>{
        console.log(this.name);
    }
}
obj.say();//mybook2
```
咦？不是说this永远指向调用者吗？这就是箭头函数的妙用。可以改变this的指向，改变成与obj同级的存在。所以window.obj.say()，与obj同级，所以就指向window对象了。
这一点常用于事件的监听，因为事件监听的时候调用者为页面的元素，而我们的变量一般都托管在页面的对象中，所以用箭头函数可以避免很多问题。
同时，箭头函数是一种简写，可以大大减少代码量。
```js
a=function (){
    return 'a'
}
a=()=>{return 'a'}
a=()=>'a'
```
以上三种形式是的效果是一致的。

## 未赋值变量给予默认值
```
function a(c='hello') {
    console.log(c)
}
a()//'hello'
a('js')//'js'
```


## 思考题
如何用es5实现es6中的let呢？