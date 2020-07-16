# js-blog-summary
对冴羽的"js深入系列"的总结，[博客地址](https://github.com/mqyqingfeng/Blog)

#### [静态作用域与动态作用域](https://github.com/mqyqingfeng/Blog/issues/3)
> js奉行的词法作用域就属于静态作用域
* 静态作用域
函数能访问哪些变量，在函数定义时就已经决定了
* 动态作用域
函数能访问哪些变量，在函数执行时才决定

bash 是动态作用域。把下面的脚本存成例如 scope.bash，然后进入相应的目录，用命令行执行 bash ./scope.bash，看看打印的值是多少。
```
value=1
function foo () {
    echo $value;
}
function bar () {
    local value=2;
    foo;
}
bar
```

#### 示例
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

---
#### [执行上下文栈](https://github.com/mqyqingfeng/Blog/issues/4)
当执行一个函数的时候，就会创建一个执行上下文，并且压入执行上下文栈，当函数执行完毕的时候，就会将函数的执行上下文从栈中弹出。

#### 示例
* 例1
```
var scope = "global scope";
function checkscope(){
    var scope = "local scope";
    function f(){
        return scope;
    }
    return f();
}
checkscope();
```
执行上下文栈的变化如下
```
ECStack.push(<checkscope> functionContext);
ECStack.push(<f> functionContext);
ECStack.pop();
ECStack.pop();
```
* 例2
```
var scope = "global scope";
function checkscope(){
    var scope = "local scope";
    function f(){
        return scope;
    }
    return f;
}
checkscope()();
```
执行上下文栈的变化如下
```
ECStack.push(<checkscope> functionContext);
ECStack.pop();
ECStack.push(<f> functionContext);
ECStack.pop();
```


作用域链，执行上下文，预编译(声明)和解释执行(运行)


执行一个函数前要做的准备工作


