---
title: JavaScript 箭头函数中的 this 问题 & 脑洞
date: 2017-08-27 16:00:00
tags:
- JavaScript
categories:
- JavaScript
---
## 问题描述
在尝试自己实现数组的高阶函数`reduce`的过程中，发现箭头函数内识别不到我想要的`this`。

用普通函数实现`reduce`是这样的：
```
Array.prototype.myReduce = function (f, initialValue) {
    console.log(this);

    let result = initialValue;
    this.forEach(elem => {
        result = f(result, elem);
    });

    return result;
}

let arr = [1, 2, 3];

let sum = arr.myReduce((a, b) => {return a + b}, 0);

console.log(sum);
// [1, 2, 3]
// 6
```
当我想写成箭头函数的形式时，出现问题：
```
Array.prototype.myReduce = (f, initialValue) => {
    console.log(this);

    let result = initialValue;
    this.forEach(elem => {
        result = f(result, elem);
    });

    return result;
}

let arr = [1, 2, 3];

let sum = arr.myReduce((a, b) => {return a + b}, 0);

console.log(sum);

// {}
// TypeError: this.forEach is not a function
```
`console.log(this)`这句的结果，在node中是一个空对象`{}`，在浏览器中则是当前的全局对象window：`Window {stop: ƒ, open: ƒ, alert: ƒ, confirm: ƒ, prompt: ƒ, …}`，无论怎样都不是预想中的调用`myReduce`这个方法的数组`arr`。看来箭头函数并不是我起初以为的仅仅是`foo function() {}`形式的简写。

## 结论

经过多方搜索，结论如下：

箭头函数和普通函数的区别在于箭头函数内部的`this`遵从词法作用域（静态作用域），也就是在这个箭头函数定义时就绑定了上下文的`this`，亦即离它最近的一个函数的`this`，如果没有，那就是全局对象，此后不会再改变。而普通函数的`this`是在运行时确定的，谁调用它，它就认谁的`this`（普通函数内除`this`之外的其他变量也遵从词法作用域原则，这也是闭包得以实现的基础）。

### 箭头函数的优点
箭头函数的这种好处在于，因为它的`this`一直都绑定了定义时的上下文，可以避免在使用「函数调用模式」调用普通函数时，`this`会被绑定到全局对象这一设计上的「错误」（因为这时候没人调用它了，见《JavaScript语言精粹》4.3节）。
```
let me = {
    name: "carol",
    introduce: function() {
        let getName = function() {
            return this.name;
        }
        console.log(getName());
    }
}
me.introduce()
// undefined
```
如果想要规避这个错误，必须这样：
```
let me = {
    name: "carol",
    introduce: function() {
        let that = this;    // 将this的值赋予一个普通变量，该变量遵从词法作用域
        let getName = function() {
            return that.name;
        }
        console.log(getName());
    }
}
me.introduce();
// carol
```
而箭头函数因为自动绑定了定义时的上下文，不用这样的hack也可以轻松访问到对象`me`的`this`。
```
let me = {
    name: "carol",
    introduce: function() {
        let getName = () => {
            return this.name;
        }
        console.log(getName());
    }
}
me.introduce();
// carol
```
### 箭头函数的缺点
普通函数的「`this`在运行时确定」这一原则，使得它可以完成开头的例子那样的功能，这一点箭头函数是做不到的，因为它的`this`在一开始就被定好了。

### 如何选择
如果这个函数不存在以后通过`call`/`apply`改变其作用域的可能，也不是定义在某个Function的原型上面预备被其他的实例调用(如开头的例子)，那么就用箭头函数。

否则的话，普通的函数我们普通地用～～～

### 吐槽
「箭头函数」这个名字听起来真是比「lambda表达式」自降气场啊。

***
## reference
1. [箭头函数 - 廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/001434446689867b27157e896e74d51a89c25cc8b43bdb3000/001438565969057627e5435793645b7acaee3b6869d1374000#0)
2. [箭头函数中this的用法.md](https://github.com/zhengweikeng/blog/blob/master/posts/2016/%E7%AE%AD%E5%A4%B4%E5%87%BD%E6%95%B0%E4%B8%ADthis%E7%9A%84%E7%94%A8%E6%B3%95.md)
3. [箭头函数中的this问题 - SegmentFault](https://segmentfault.com/q/1010000006683698)