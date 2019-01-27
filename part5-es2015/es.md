
# 关于js基础
关于js我并不想多讲。因为本文的目的，始终都是基于校招员工的角度出发的。所以本文假设至起码会C或者JAVA的一种
而关于语言，其实来来去去无非几样
1. 变量的定义
2. 循环
3. 函数
4. 类与类的组织方式
其实每一种语言都是在上述几个要素上演变出来的。
对于已经掌握一门类C语言来说，从头开始看js怕是不大合适的。所以本文就简洁的介绍一下js的特性，之后就可以看代码了。
JS对于C、Java的特别之处就是函数。

## js中的函数
首先说明JavaScript是和Java没有关系的。
当年sun公司收购了JavaScript的时候为了蹭一下java的热度所以把这门语言命名为JavaScript。
### this指向
this永远指向调用者
```js
var obj = {
  name : "mybook",
  say : function(){
    console.log(this.name);//mybook
  }
}
obj.say();//合乎其他语言的逻辑，输出mybook

var name = 'mybook';
function say(){
  console.log(this.name);//mybook
}
say();//等价于window.say(),调用者为window

var name = 'mybook';
var obj = {
  name : "mybook2",
  say : function(){
    console.log(this.name);//mybook
  }
}
var fun = obj.say;
fun();//等价于window.fun，调用者为window
```


### 函数被返回
来看下面的一段代码
```js
var a = function fun1(y) {
    var x = 'hello';
    return function fun2(z){
        console.log(x)
        console.log(y)
        console.log(z)
    }
}
a('world')('javascript')
```
猜想一下输出的结果？
hello world javascript undefined
以上是一段很灵活的js代码，这个代码有以下几点特别之处。
1. x能被打印出来，说明fun2中可以访问fun1中的值。但是你发现没有，在fun2执行的时候，明明fun1已经执行了返回，在其他语言中，理论上var x 已经被销毁了，不会被fun2访问得到了。为什么呢？js的特性可以使这个函数执行完了之后变量给你保留一下，不会失效。
2. 函数可以像普通变量一样被返回，被赋予变量。函数是js中的一等公民，任何情况都可以当做变量。

以上这个特性称之为闭包，在深入讲解闭包之前，我们再来讲下this。

### this指向2
我们再来看下返回后的函数this的指向
```js
var name = 'mybook';
var obj = {
  name : "mybook",
  say : function(){
    return function(){
      console.log(this.name);//mybook
    }
  }
}
obj.say()();//obj.say()返回了一个函数xxx，然后再window.xxx(),this指向window
li.onclick = function(){
    console.log(this);//this指向li本身，因为li是函数的调用者。
}
```
由于函数可以被返回，所以this的指向相当灵活，既可以指向window对象也可以指向对象本身。

## 深入理解闭包
```js
function fun1(){
    var arr = [];
    for(var i=0;i<5;i++){
        arr[i] = function(){
            return i;                         
    }                                           
    return arr;
}
console.log(fun1()[0]())
```
你会发现，居然打印出来的值是5。为什么？因为arr[1]中的function并未声明变量i，所以只能去fun1里找。不过这个时候fun1其实已经是执行完毕的了，只不过变量需要保留，所以呢，把这个i给你留住了使得arr中的函数可以访问而已，所以返回5。
先说解决方案，
```js
function fun1(){
    var arr = [];
    for (var i = 0; i < 5; i++) {
        arr[i] = (function fun2 (num) {
            return function () {
                return num
            }
        })(i)
    }
    return arr;
}
console.log(fun1()[0]())
```
是不是有点晕？说实话，第一次看这几行的代码就跟天书一样。
我们为了解决作用域的问题，所以呢，就想传入i给我们的arr数组里面的函数。
形如（function fun2（））()的东西我们叫做立即执行函数，就是我们创建了一个函数然后立即执行一下。在上述代码中，创建了一个函数fun2来接收外部传入的变量i。这个时候i是在fun1的作用域中，所以它的值就是真真实实的在函数fun1中的变量，而非fun1执行完的变量。
其次，我们修bug的时候，不能改变原有的返回形式，以前返回的是函数，现在我们也要返回一个函数，所以呢，fun2又return出一个函数这个函数就return我们fun2里面的num。
你也许会问，这个返回的函数不会访问fun2中最终态的num吗？会的，不过fun2的最终态就是2（因为fun2形成阶段传入的值是2，而fun2并未对传入的参数做出修改），所以得到期望的结果。
## 总结
js是不是有点反人类？各种因为作用域的问题，导致多层嵌套的return，this的指向比较蛋疼，真的好烦。  
不要气馁，我们下一节讲一下其他更优的解决方案。
额外提醒多一句，这个内容几乎是h5面试的必考题，所以this的指向和闭包的内容是一定要掌握的。