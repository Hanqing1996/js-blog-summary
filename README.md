# js-blog-summary
对冴羽的"js深入系列"的总结，[博客地址](https://github.com/mqyqingfeng/Blog)

#### 静态作用域与动态作用域
> js奉行的词法作用域就属于静态作用域
* 静态作用域
函数能访问哪些变量，在函数定义时就已经决定了
* 动态作用域
函数能访问哪些变量，在函数执行时才决定

####
```
var value = 1;

function foo() {
    console.log(value);
}

function bar() {
    var value = 2;
    foo();
}

bar();
```
// 结果是 ???
假设JavaScript采用静态作用域，让我们分析下执行过程：

执行 foo 函数，先从 foo 函数内部查找是否有局部变量 value，如果没有，就根据书写的位置，查找上面一层的代码，也就是 value 等于 1，所以结果会打印 1。

假设JavaScript采用动态作用域，让我们分析下执行过程：

执行 foo 函数，依然是从 foo 函数内部查找是否有局部变量 value。如果没有，就从调用函数的作用域，也就是 bar 函数内部查找 value 变量，所以结果会打印 2。

前面我们已经说了，JavaScript采用的是静态作用域，所以这个例子的结果是 1。
