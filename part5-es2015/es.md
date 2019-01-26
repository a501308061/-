
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
来看下面的一段代码
```js
var a = function fun1(y) {
    var x = 'hello';
    return function fun2(z){
        console.log(x)
        console.log(y)
        console.log(z)
        console.log(this.x)
    }
}
a('world')('javascript')
```
猜想一下输出的结果？
hello world javascript undefined
以上是一段很灵活的js代码，这个代码有以下几点特别之处。
1. x能被打印出来，说明fun2中可以访问fun1中的值。但是你发现没有，在fun2执行的时候，明明fun1已经执行了返回，在其他语言中，理论上var x 已经被销毁了，不会被fun2访问得到了。
2. this.x 变成了undefined。说明函数中的this指向会发生变化。
3. 函数可以像普通变量一样被返回，被赋予变量。函数是js中的一等公民，任何情况都可以当做变量。

以上这个特性称之为闭包
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
但是这鸡儿也太反人类了吧。
先说解决方案，
```js
function fun1(){
    var arr = [];
    for(var i=0;i<5;i++){
        arr[i] = (function(num){
            return function(){
            
            }                         
    })[i]                                           
    return arr;
}
console.log(fun1()[0]())
```