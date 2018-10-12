---
title: 每天学点js-构造器调用方式与闭包
comments: true
date: 2016-8-7 10:55
categories: [js]
tags: [每天学点js,js]
keywords: 构造器调用,闭包
---


### 构造器方式调用: 使用 `new` 关键字  
> JS是一门基于原型继承的语言，对象可以直接从其他对象继承属性。该语言是无类型的。
> 构造器函数：函数创建的目的是结合new前缀来调用，那它就被称为构造器函数。按照约定，它们保存在以大写格式命名的变量里。
> 构造器函数缺点：如果调用构造器函数时，没有在前面加上new，可能会产生非常糟糕的事情，即没有编译时警告，也没有运行时警告，所以约定非常重要。不推荐使用这种形式的构造器函数,推荐使用下面的闭包

```js
var Quo = function(str){ //创建一个名为Quo的构造器函数，它创建一个带有status属性的对象。
    this.status = str;
};

//给Quo的所有实例提供一个getStatus的公共方法
Quo.prototype.getStatus = function(){
  return this.status;
};
//构造一个Quo实例
var myQuo = new Quo("confused");
console.log(myQuo.getStatus()); //打印显示"confused"

```

- 此时同样可以通过 `myQuo.status` 来获取status的值, 无法做到私有成员变量
- 并且可以通过 `myQuo.status = 1` 来改变myQuo对象的属性值


### 使用闭包
> JS作用域：不支持块级作用域,支持函数作用域。定义在函数中的参数和变量在函数外部不可见，而在函数内部任何位置定义的变量，在该函数内部任何地方都可见。由于JS缺少块级作用域，所以不建议延迟声明变量，最好的做法是在函数体的顶部声明函数中可能用到的所有变量。
> 闭包的好处是内部函数可以访问定义它们的外部函数的参数和变量。当内部函数拥有比它的外部函数更长的生命周期时，内部函数引用的外部函数变量不会被释放（Java中一般会引起内存泄漏，而JS闭包恰好利用该特性）。

1. 闭包示例:

```js
//创建一个myObj对象，把匿名函数调用结果赋值给它。
var myObj = (function(){
  var value = 0;//由于函数作用域，外部对value的操作只能基于return的对象的两个方法。
  //该匿名函数返回一个包含两个方法的对象，并且这些方法继续享有访问value变量的特权。
  return {
    increment: function(inc){
      value += typeof inc === 'number' ? inc : 1;
    },
    getValue: function(){
      return value;
    }
  };
}());

```

2. 重构Quo构造器：

```js
// 将status作为私有属性，提供对应的getter方法(原版本直接可以访问status，提供getter没意义，还需要显式new)。
var quo = function(status){

  var flowNo = new Date().getTime();
  return {
    getStatus: function(){
      return status;
    },
    getFlowNo: function() {
      return flowNo;
    }
  };
};
var myQuo = quo("amazed"); // 由于不需要加上new，所以名字没有首字母大写
console.log(
  myQuo.getStatus(),
  myQuo.getFlowNo()
);

setTimeout(function(){
  var b = quo("bbb"); 
  console.log(
    b.getStatus(),
    b.getFlowNo()
  );
}, 1000)

```

- 此时不能通过 `myQuo.status`, `myQuo.flowNo` 来获取相应的属性值
- 通过 `myQuo.status = 1`, `myQuo.flowNo = '12345'` 这种方式也没有改变原来的属性,只是添加了新属性
- 注：当调用quo时，它返回一个包含get_status方法的新对象。该对象的一个引用保存在myQuo中。即使quo已经返回，但get_status方法仍然享有访问quo对象的status属性的特权。get_status方法并不是访问该参数的一个副本，而是参数本身。因为该函数可以访问它被创建时所处的上下文环境，这被称为闭包。

3. 加深闭包理解的例子

```js
// 糟糕的例子
//构造一个用于显示节点序号的函数，但由于用错误的方式给数组中的节点设置事件处理函数，而造成每次点击总是显示节点的数目
var add_the_handlers = function(nodes){
    var i;
    for(i=0; i<nodes.length; i+=1){
        nodes[i].onclick = function(e){
            alert(i);
        };
    }
};

// 改良后的例子
var add_the_handlers = function(nodes){
    var helper = function(i){
        return function(e){
            alert(i);
        };
    };
    var i;
    for(i=0; i<nodes.length; i+=1){
        nodes[i].onclick = helper(i);
    }
};


```

注：避免在循环中创建函数，它可能只会带来无谓的计算，还会引起混淆。建议先在循环之外创建一个辅助函数，让辅助函数返回一个绑定当前i值的函数，这样就不会导致混淆。


### 另一个关于立即执行函数的例子

1. 预期1秒后输出0~9, 实际输出10次 10

```js
for(var i = 0; i < 10; i++){
  setTimeout(function(){
    console.log(i);
  }, 1000);
}
```

2. 改进方法1(一般的做法):

```js
for(var i = 0; i < 10; i++){
  (function(_i){
    setTimeout(function(){
      console.log(_i);
    }, 1000);
  })(i);
}
```

3. 改进方法2(根据上面闭包突然想到的尝试,可行):

```js
var _helper = function(i) {
  setTimeout(function(){
    console.log(i);
  }, 1000);
};

for(var i = 0; i < 10; i++){
  _helper(i);
}
```

### 闭包延伸: 模块
> 模块：为了屏蔽JS全局变量的使用，我们可以使用函数和闭包来构造模块。模块是一个提供接口却隐藏状态与实现的函数或对象。

> 模块模式：模块模式利用了函数作用域和闭包来创建对象与私有成员的关联。在下面示例中，只有deentityify方法有权访问字符实体表这个数据对象。

> 模块模式的一般形式：一个定义了私有变量和函数的函数; 利用闭包创建可以访问私有变量和函数的特权函数; 最后返回这个特权函数，或把它们保存到一个可访问的地方。

> 模块模式好处：使用模块模式可以摒弃全局变量的使用。模块模式促进信息隐藏和其他优秀的设计实践，比如单例模式，利于应用程序的封装。模块模式也可以用来产生安全的对象，比如下面示例中用来产生序列号的对象。

```js

var serial_marker = function(){
  var prefix = '';
  var seq = 0;
  return {
      set_prefix: function(p){
          prefix = String(p);
      },
      set_seq: function(s){
          seq = s;
      },
      gensym: function(){
          var result = prefix + seq;
          seq += 1;
          return result;
      }
  };
};
// 虽然seqer可变，且可以替换它的方法，但替换后的方法依然不能访问私有成员。
var seqer = serial_marker();
seqer.set_prefix('Q');
seqer.set_seq(1000);
var unique = seqer.gensym(); //unique='Q1000'

```

### **记忆**(不太理解使用场景)
> 记忆：函数可以将先前操作的结果记录在某个对象里，从而避免无谓的重复运算，这种优化被称为记忆。JS的对象和数组能很方便的实现这种优化。

带记忆功能的函数：memoizer
```js
//memoizer取得一个初始的memo数组和formula函数，返回一个管理memo存储和调用formula的recur函数。
//把这个recur函数和它的参数传递给formula函数。
var memoizer = function(memo, formula){
  var recur = function(n){
      var result = memo[n];
      if(typeof result !== 'number'){
          result = formula(recur, n);
          memo[n] = result;
      }
      return result;
  };
  return recur;
};
//应用memoizer产生可记忆的阶乘函数
var factorial = memoizer([1, 1], function(recur, n){
    return n * recur(n-1);
});

```



### 参考
> (JavaScript语言精粹)[https://github.com/qibaoguang/Study-Step-by-Step/blob/master/%E8%AF%BB%E4%B9%A6%E7%AC%94%E8%AE%B0/javascript_the_good_parts.md]

