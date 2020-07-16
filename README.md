# js-blog-summary
对冴羽的"js深入系列"的总结，[博客地址](https://github.com/mqyqingfeng/Blog)

## [静态作用域与动态作用域](https://github.com/mqyqingfeng/Blog/issues/3)
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
## [执行上下文栈](https://github.com/mqyqingfeng/Blog/issues/4)
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
---
## [作用域链](https://github.com/mqyqingfeng/Blog/issues/6)
当查找变量的时候，会先从当前上下文的变量对象中查找，如果没有找到，就会从父级(词法层面上的父级)执行上下文的变量对象中查找，一直找到全局上下文的变量对象，也就是全局对象。这样由多个执行上下文的变量对象构成的链表就叫做作用域链。

#### 函数的内部属性 [[scope]]
当函数创建的时候，就会保存所有父变量对象到其中，你可以理解 [[scope]] 就是所有父变量对象的层级链，但是注意：[[scope]] 并不代表完整的作用域链！

#### 函数执行上下文中作用域链和变量对象的创建过程：
* 示例
```
var scope = "global scope";
function checkscope(){
    var scope2 = 'local scope';
    return scope2;
}
checkscope();
```
执行过程如下：

1.checkscope 函数被创建，保存作用域链到内部属性[[scope]]
```
checkscope.[[scope]] = [
    globalContext.VO
];
```
2.创建 checkscope 函数执行上下文，checkscope 函数执行上下文被压入执行上下文栈
```
ECStack = [
    checkscopeContext,
    globalContext
];
```
3.checkscope 函数执行前，需要初始化 checkscopeContext，第一步：设置 checkscopeContext 的 Scope 属性，即复制函数[[scope]]属性创建作用域链
```
checkscopeContext = {
    Scope: checkscope.[[scope]],
}
```
4.第二步：用 arguments 创建活动对象，随后初始化活动对象，加入形参、函数声明、变量声明
```
checkscopeContext = {
    AO: {
        arguments: {
            length: 0
        },
        scope2: undefined
    }，
    Scope: checkscope.[[scope]],
}
```
5.第三步：将活动对象压入 checkscope 作用域链顶端
```
checkscopeContext = {
    AO: {
        arguments: {
            length: 0
        },
        scope2: undefined
    },
    Scope: [AO, [[Scope]]]
}
```
6.准备工作做完，开始执行函数，随着函数的执行，修改 AO 的属性值
```
checkscopeContext = {
    AO: {
        arguments: {
            length: 0
        },
        scope2: 'local scope'
    },
    Scope: [AO, [[Scope]]]
}
```
7.查找到 scope2 的值，返回后函数执行完毕，函数上下文从执行上下文栈中弹出，checkscopeContext 被销毁。直到下次 checkscope 再次运行，重复步骤2-7
```
ECStack = [
    globalContext
];
```
---
## [闭包](https://github.com/mqyqingfeng/Blog/issues/9)



