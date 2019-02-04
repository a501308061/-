# 新增对象
es6还新增了一些对象。
## Promise对象
```js
a=new Promise((reslove,reject)=>{
    setTimeout(resolve(1), 1000)
})
```
es6中Promise是异步回调的一种解决方案。
简单说来就是Promse对象在构建的时候接受一个函数（为了避免和后面的函数混淆，我们称之为构建参数函数）。
这个函数接受两个参数，一个是reslove，一个是reject，这两个参数都是函数。Promise的构建参数函数可以在适当的时机触发reslove和reject。
上述例子就是在1s后触发reslove的操作,然后就可以用如下方式调用
```
a.then((value)=>{
    console.log(value) //1
    return 1+value
}).then((value2)=>{
    console.log(value2)//2
})
```
当构建参数函数触发reslove的时候，则Promise对象就会触发then中的函数，并将传入resolve的值作为value传入then。此外then可以被链式调用，直至最后一个then被执行完毕。
同理，触发reject的时候就会调用catch
Promise对象常用于接口调用的时候
```js
const callApiPost = (options) => {
    return new Promise((reslove,reject)=>{
         urlPost(options,function (result) {
          if (result.exception){
            reject(result.exception)
          }else{
            reslove(JSON.parse(result.body))
          }
      })
  })
}

callApiPost().then(()=>console.log("success")).catch(()=>console.log("fail"))
```

## Set与Map对象
Set对象相当于一个去重的数组（想一下数学里面的集合概念）
```js
const set = new Set([1, 2, 3, 4, 4，4]);
```
set中此时只包含四个元素（是哪四个我就不说了）
可以轻易的实现数组去重
```
const set = new Set([1, 2, 3, 4, 4，4]);
a=[...set]
```
Set对象拥有key，value，forEach的方法。不过由于本身没有键值，所以，Set.key和Set.value的行为是一致的（即取键值和键名都返回集合中的元素）。

Map对象相当于对象，不过这个对象的键名可以放入任何你想放的东西
如
```js
const map = new Map([
  ['name', '张三'],
  ['title', 'Author']
]);
map.size // 2
map.has('name') // true
map.get('name') // "张三"
map.has('title') // true
map.get('title') // "Author"
```

Map对象也拥有key，value，forEach这里就不再叙述了

本节的重点在于Promise，其余两种数据对象在笔者的工作中也比较少用到。此外，es6还增加了如Symbol、WeakMap、WeakSet等对象。
至此，es6的介绍便告一段落了。虽然仅一小部分，要么就是相当简单的一看就懂的，如字符串``，export、import，要么就是工作中比较少用到的如Proxy等。
不过上述已经是将es6精简至工作中必然会用到的方法了，请务必掌握。
各位有兴趣的可以阅读《深入理解ES6》（Nicholas著）和《ES6标准入门》（阮一峰著）
本文的例子也源于这两本书。（其实es6的书籍市面上仅有这两本）