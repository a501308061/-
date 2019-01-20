
# 关于js基础
关于js我并不想多讲。因为本文的目的，始终都是基于校招员工的角度出发的。所以本文假设至起码会C或者JAVA的一种
而关于语言，其实来来去去无非几样
1. 变量的定义
2. 循环
3. 函数
4. 类与类的组织方式
其实每一种语言都是在上述几个要素上演变出来的。
而JS对于C、Java的特别之处就是函数即对象。

## js中的函数
首先说明JavaScript是和Java没有关系的。
当年sun公司收购了JavaScript的时候为了蹭一下java的热度所以把这门语言命名为JavaScript。
来看下面的一段代码
```js
function fun1(y){
    var x='hello'
    retrun fun2(z){
        console.log(x)
        console.log(y)
        console.log(z)
        console.log(this.x)
    }
}
fun1('world')('javascript')
```
猜想一下输出的结果？
hello world javascript undefined
以上是一段很灵活的js代码，这个代码有以下几点特别之处。
1. x能被打印出来，说明函数2中可以访问函数1中的值。但是调用函数2的环境，