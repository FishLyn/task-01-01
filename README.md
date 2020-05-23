# 作业

## 第一题

1. 请说出下列最终的执行结果，并解释为什么?

   ```javascript
   var a = []
   for (var i = 0; i < 10; i++){
       a[i] = function () {
           console.log(i)
       }
   }
   a[6]()
   ```

   答：10

   理由：for循环条件定义了全局的变量 i，当循环结束的时候，i 被累计到 10，此时调用 a 数组内的方法的时候，方法体内的 i 会去寻找谁定义了它，最终找到定义在全局的变量 i，此时 i 为 10， 所以会打印 10

    ```javascript
   // 扩展知识
   // 若想解决全局变量造成的这种问题，在ES5之前，可以使用闭包的机制解决，用函数作用域拜托全局作用域的限制
   var a = []
   for (var i = 0; i < 10; i++){
       (function (i) {
           a[i] = function () {
               console.log(i)
           }
       })(i)
   }
   a[6]() // 6
   // 在for循环体内，定义了立即执行的函数，每次循环的时候都会执行函数，将 i 传入函数作用域内，此时当调用数组内的函数的时候，会向上作用域找定义的变量，会在函数作用域内找到定义变量，而函数作用域内的变量不受全局的影响，所以每次调用都能获取到对应的值
   
   // 在es6之后新增了块级作用域的概念，因此也可以用块级作用域来拜托全局的影响
   var a = []
   for (let i = 0; i < 10; i++){
       a[i] = function () {
           console.log(i)
       }
   }
   a[6]() // 6
   // 这种方法和闭包是类似的，块级作用域内的变量也不受全局影响，函数调用的时候变量向上寻找定义，会先在块级作用域内找到，从而获取对应的值
    ```

   

## 第二题

2. 请说出下列最终的执行结果，并解释为什么?

   ```javascript
   var tmp = 123
   
   if (true) {
       console.log(tmp)
       let tmp
   }
   ```

   答：会报变量未定义的错

   理由：网上有很多关于块级作用域是否会提升的争论，一方认为会提升，理由是在定义前引用的话，会报未定义的错误，而不是找到全局定义的变量呢，一方认为不会提升，理由是如果提升了为什么会报未定义的错，而不是undefined呢。

   而我经过不专业的思考后，是这么理解的：

   函数在执行前，会为所有的变量开辟内存空间，以便在执行开始前就判断是否有定义重复变量的问题。

   而var 和 let 因为作用域的不同，会开辟不同的内存空间。

   开辟内存空间后，因为 var 的机制，会将所有 var 定义的变量提升到最上面进行变量申明，而 let 不会，仍然是开辟了内存空间，但是未申明的情况。

   当变量引用的时候，会优先在当前的作用域内寻找定义的变量。

   在这道题中，会优先找到在块级作用域内的 tmp 变量，但是此时变量未申明，因此会报未定义的错误，而var不会有这种情况

   不知道这种理解对不对



## 第三题

3. 结合 ES6 新语法，用最简单的方式找出数组中的最小值?

   ```javascript
   var arr = [12, 34, 32, 89, 4]
   ```

   答：Math.min(...arr)

   ```javascript
   // 扩展知识
   // 多种求数组最小值的方法，省略上述的方法
   // 第一种: Math.min.apply() 在ES5之前最快捷的方式
   console.log(Math.min.apply(null, arr))
   // 解析：因为 Math.min() 是通过传入多个参数进行比较出最小值的方法，所以在还没有解构之前，可以通过用apply调用的方式，将所有参数以数组的形式传入，从而达到解构的目的
   
   // 第二种：Array.reduce()
   const res = arr.reduce((prev, current) => {
   	return Math.min(prev, current)
   })
   console.log(res) // 4
   // 解析：通过 reduce 方法，对比上一个返回的值和当前值判断最小值，并返回其中更小的值和下一个进行对比
   
   // 第三种： Array.sort()
   const res = arr.sort((a, b) => a - b)
   console.log(res[0]) // 4
   // 解析：通过sort方法对数组进行升序排序，取数组第一位的数据为最小值
   
   // 第四种：通过循环比较的方法
   let min = arr[0]
   for(const item of arr) {
       if (item < min) min = item
   }
   console.log(min)
   // 解析：设定数组的第一个数据为最小值，赋值给一个变量，循环数组内的成员，与最小值变量比对，更小的重新给变量赋值，循环结束后变量即为最小值
   
   // 第五种：邪教方法 toString,join
   console.log(eval('Math.min('+ arr.toString() +')'))
   console.log(eval('Math.min('+ arr.toLocaleString() +')'))
   console.log(eval('Math.min('+ arr.join() +')'))
   // 解析：用 toString,toString,join 强行转换成用逗号分割的字符串，并放入Math.min()的参数位置，组合成新的字符串，注意是字符串而不是直接当成参数传入Math.min方法，再用eval函数强行执行这个字符串内的Javascript代码
   ```

   

## 第四题

4. 请详细说明 var，let，const 三种声明变量的方式之间的具体差别?

   答：在 es6 之前，所有定义变量都是用的 var，var 用于定义全局变量，且在代码执行开始的时候，会将所有用 var 定义的变量提升到函数开始执行的时候申明，也就是代码提升

   es6 的时候，新增了两个定义的关键词，let，const，也引申了新的作用域，块级作用域，let，const只在块级作用域内有效果，并且没有变量提升已经有暂时性死区的约束，即 let，const不能在引用之后才定义

   let 和 var 一样，都是可以定义变量的，定义的变量可以修改，并且可以只申明，不赋值

   const 定义的常量，必须赋值，并且值不能修改，但是const定义的对象数据可以修改内部的数据，因为修改的是内容，而不是数据的引用地址

   总结：var    申明全局变量，在全局作用域中起作用，可以修改，有变量提升

   ​			let     申明局部变量，在块级作用域中起作用，可以修改，受暂时性死区的约束，无法在引用后才定义

   ​			const 申明局部变量，在块级作用域中起作用，不可以修改引用地址，受暂时性死区的约束，无法在引用后才定义



## 第五题

5. 请说出下列代码最终输出的结果，并解释为什么?

   ```javascript
   var a = 10
   var obj = {
       a: 20,
       fn () {
           setTimeout(() => {
               console.log(this.a)
           })
       }
   }
   obj.fn()
   ```

   答：20

   理由：首先分析传入计时器的参数是箭头函数，而箭头函数不会改变this的指向，所以当obj调用内部函数的时候，此时this会指向 function 函数 fn 的调用者 obj，所以此时 this.a === 20

   ```javascript
   // 扩展知识
   // 将箭头函数改成function试下
   var a = 10
   var obj = {
       a: 20,
       fn () {
           setTimeout(function () {
               console.log(this.a)
           })
       }
   }
   obj.fn()
   // 此时无论fn的调用者是谁，this都会指向setTimeout的调用者，全局对象
   // 在浏览器环境下，全局对象是Window，此时打印出来的就是 10
   // 但是在node环境下，全局对象 global，此时打印出来的是 undefined
   ```

   

## 第六题

6. 简述 `Symbol`类型的用途?

   答：Symbol 是 es6 新增的基础数据类型，用于表示唯一的值，每个 Symbol() 返回值都是新的，目前 Symbol 最主要的用途是给对象定义唯一的属性。

   ```javascript
   // 给对象添加Symbol属性
   const name = Symbol()
   const obj = {
       [name]: 'foo'
   }
   // 要想获取该属性的值，只能通过对象内的方法
   const obj = {
       [name]: 'foo',
       say () {
           return this[name]
       }
   }
   console.log(obj.say()) // foo
   // 用Symbol定义的属性适合作为该对象的私有属性
   for (let item in obj) {
       console.log(item)
   }
   console.log(Object.keys(obj))
   console.log(JSON.stringify(obj))
   // 用以上方法都无法获取到Symbol定义的属性，要想获取到，需要使用Object.getOwnPropertySymbols()
   console.log(Object.getOwnPropertySymbols(obj)) //能获取到所有的Symbol属性
   console.log(Reflect.ownKeys(obj)) // 能获取到所有的属性，包括Symbol
   ```

   

## 第七题

7. 说说什么是浅拷贝，什么是深拷贝?

   答：浅拷贝和深拷贝是针对复杂对象才有的对比，简单的说就是

   浅拷贝只是复制了对象的值的引用地址，修改复制的对象会影响到源对象，而深拷贝是是复制了完全不同的新的值，修改复制对象不会影响源对象

   ```javascript
   // 深浅拷贝的研究，该对比只针对对象
   let obj1 = {
       a: 1,
       b: 2,
       c: {
           d: 3
       }
   }
   let obj2 = {}
   
   // 直接赋值
   obj2 = obj1
   console.log(obj2 === obj1) // true 这种情况其实不是拷贝的应用，只是让另一个对象的属性也指向了相同的内存地址
   
   // 浅拷贝实现方式
   // 1. Object.assign()
   obj2 = Object.assign({}, obj1)
   obj2.a = 2
   obj2.c.d = 4
   console.log(obj1) // { a: 1, b: 2, c: { d: 4 } }
   console.log(obj2) // { a: 2, b: 2, c: { d: 4 } }
   // 可以看到源对象 obj1 的属性a未被修改，属性c的值被修改了
   // 从这里可以看出 Object.assign() 是浅拷贝，因为它无法完整的拷贝出对象的值，第二层引用还是指向了源对象
   
   // 2. Array.prototype.concat()
   let arr1 = [1, 2, {a: 2}]
   let arr2 = arr1.concat()
   arr2[0] = 10
   arr2[2].a = 5
   console.log(arr1) // [ 1, 2, { a: 5 } ]
   console.log(arr2) // [ 10, 2, { a: 5 } ]
   // 同样第二层仍是浅拷贝
   
   // 3. Array.prototype.slice()
   let arr1 = [1, 2, {a: 2}]
   let arr2 = arr1.slice()
   arr2[0] = 10
   arr2[2].a = 5
   console.log(arr1) // [ 1, 2, { a: 5 } ]
   console.log(arr2) // [ 10, 2, { a: 5 } ]
   // 同样的问题
   
   // 深拷贝实现方式
   // 1. JSON.parse(JSON.stringify())
   obj2 = JSON.parse(JSON.stringify(obj1))
   obj2.a = 10
   obj2.c.d = 5
   console.log(obj1) // { a: 1, b: 2, c: { d: 3 } }
   console.log(obj2) //{ a: 10, b: 2, c: { d: 5 } }
   // JSON.stringify 可以将对象解析成 JSON 字符串， 而 JSON.parse 可以将 JSON 字符串解析成对象，所以这么一来一回生成的对象和源对象是完全没有关系的
   
   // 2. 手写递归方法
   // 由上述浅拷贝的执行结果可以看出，对象的值为基本数据类型的时候是进行的深拷贝，而引用类型的时候是浅拷贝，那么我们只需要不断递归引用类型，直到都是基本数据类型的时候，这时候拷贝的对象就是深拷贝对象了
   function checkType (target) { // 判断参数是object 还是 array
       return Object.prototype.toString.call(target).slice(8, -1)
   }
   function clone(target) {
       let result, targetType = checkType(target)
       if (targetType === 'Object') {
           result = {}
       } else if (targetType === 'Array') {
           result = []
       } else {
           return target
       }
       for (let i in target) {
           let value = target[i]
           if (checkType(value) === 'Object' || checkType(value) === 'Array') {
               result[i] = clone(value)
           } else {
               result[i] = value
           }
       }
       return result
   }
   obj2 = clone(obj1)
   obj2.a = 10
   obj2.c.d = 5
   console.log(obj1) // { a: 1, b: 2, c: { d: 3 } }
   console.log(obj2) // { a: 10, b: 2, c: { d: 5 } }
   
   // 3. lodash
   // 借助第三方函数库 lodash 的 cloneDeep 方法
   ```

   

## 第八题

8. 谈谈你是如何理解 JS 异步编程的，Event Loop 是做什么的，什么是宏任务，什么是微任务?

   答：javascript 是门单线程的语言，之所以设计成这样，在语言设计之初，javascript就是脚本语言，并不需要处理多复杂的任务，当如今发展起来之后，再想修改会变的很麻烦。而单线程的缺点就是，如果执行某个耗时的任务，则会阻塞整个进程，所以为了解决这个问题，我们需要使用异步编程，当遇到耗时的任务的时候，我们可以将这段代码放到任务队列中等待稍后执行，这样主线程的就能够继续执行下面的代码，大大提高代码执行效率。

   而当主线程上的所有同步任务执行后，要怎么将在任务队列中的的任务发到主线程继续执行呢，就需要用到Event Loop这个工具，监听主线程的任务执行情况，当监听到所有任务都完成后，则会将消息队列中等待的任务依次发到主线程执行，执行完一个发一个

   宏任务，即参与到事件循环的任务

   微任务，不参与到事件循环的任务，会在主线程任务执行完之后立即执行，而宏任务要等微任务都执行完之后依次执行

   

## 第九题

9. 将下面异步代码使用 Promise 改进?

   ```javascript
   setTimeout(function () {
       var a = "hello "
       setTimeout(function () {
           var b = "lagou "
           setTimeout(function () {
               var c = "I ❤ U"
               console.log(a + b + c)
           }, 10)
       }, 10)
   }, 10)
   ```

   答：

   ```javascript
   Promise.resolve()
   	.then(function (res) {
          return new Promise((resolve) => {
              setTimeout(() => {
                  resolve('hello')
              }, 10)
          })
   	})
   	.then(function (res) {
           return new Promise((resolve) => {
               setTimeout(() => {
                   resolve(`${res} lagou`)
               }, 10)
           })
   	})
   	.then(function (res) {
       	setTimeout(() => {
               console.log(`${res} I ❤ U`)
           }, 10)
       })
   ```

   

## 第十题

10. 请简述 TypeScript 与 JavaScript 之间的关系?

    答：TypeScript 是 JavaScript 的超集，是对 JavaScript 的一个补充，TypeScript 不仅包含了 Javascript 原有的特性，还增加了强大的类型系统和对 EMCAScript 新特性的支持，最终 TypeScript 还是会被编译成 JavaScript 在浏览器或者node下运行



## 第十一题

11. 请谈谈你所认为的 TypeScript 的优缺点

    答

    优点：

    	1. TypeScript 对变量，函数等进行了类型限制，使得代码的可读性，稳定性都有一定的提升
     	2. TypeScript 新增的像枚举类型，接口，抽象类，泛型等有时候会让代码编写更顺畅

    缺点：

    1. 增加了代码量，在项目开发初期，进度会比较慢，但是如果是一个长期开发维护的项目，后期会大大降低维护成本
    2. 增加了学习成本，像是枚举，接口，泛型等需要经过一定的学习之后才能熟练运用

















