
本篇主要谈谈PHP中闭包函数。

-------------------------

闭包函数(Closures)又叫做匿名函数(Anonymous functions)，也就是没有定义名字的函数。主要用作回调函数的参数值。闭包在 PHP5.3 中被引入。闭包使用的语法和普通函数相同，但是他其实是伪装成函数的对象，是 Closure 类的实例。闭包和字符串或整数一样，是等值类型。

## 1.创建闭包

### 1)没有参数的闭包

```
$closure = function () {
  echo "没有参数的闭包";
};

// 调用闭包
closure();  // 这样就调用了闭包，输出"没有参数的闭包"

```

更多的时候是作为回调函数来使用:

```
function useClosure (Closure $callback) {
    return $callback();
}

// $closure作为参数传递给函数useClosure，如果是普通函数是没有办法作为useClosure的参数的
useClosure($closure);

```

也可以不通过变量赋值，直接使用。例如:

```
function useClosure (function () {
  echo "没有参数的闭包";
}) ;

```

闭包不仅可以做函数的参数，也可以作为函数的返回值。例如:

```
function getClosure () {
    return function () { echo "没有参数的闭包"; };
}

// 调用函数
getClosure();  // 输出"没有参数的闭包"

```

### 2)有参数的闭包

```
<?php

$greet = function($name)
{
    printf("Hello %s", $name);
};

$greet('World');  //输出"Hello World"
$greet('PHP');    //输出"Hello PHP"
?>

```

## 2.从父作用域中继承变量

闭包可以从父作用域中继承变量。 任何此类变量都应该用**use**关键字。PHP 7.1 起，不能传入此类变量： superglobals、 $this 或者和参数重名。

```
<?php
$message = 'hello';

// 没有使用"use"关键字
$example = function () {
    var_dump($message);  // 报错"Notice: Undefined variable: message in /example.php on line 6"
};
echo $example();      // 输出null


// 继承 $message
$example = function () use ($message) {
    var_dump($message);
};
echo $example();      // 输出'hello'

// 继承变量的值来自定义函数时的值，而不是调用时的值
$message = 'world';
echo $example();      // 还是输出'hello'

?>

```

要如何解决改变变量的值之后，调用使用该变量的闭包函数也对应使用改变后的变量的值呢？这里就需要用到PHP的引用。即在变量前加&符号。

```
<?php
$message = 'hello';

// 通过引用继承
$example = function () use (&$message) {
    var_dump($message);
};
echo $example();      // 输出'hello'

// 改变变量的值后调用闭包
$message = 'world';
echo $example();      // 这时就可以输出'world'

?>

```

如果我想既使用父作用域中已经定义好的变量，又想传递参数时应该怎么办呢？请看下面这个例子，两个都解决了。

```
<?php
$message = 'hello';

$example = function ($arg) use ($message) {
    var_dump($arg . ' ' . $message);
};
$example("hello");   // 输出'hello world'

?>

```
