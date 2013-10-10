Qunit Cookbook 中文版
=============

#介绍
自动化测试软件是一个重要的开发工具。单元测试是自动化测试的基本组成元素：软件的每个组件，即单元，伴随着一个可以由TestRunner运行一遍又一遍而无须人工干预的测试。换句话说，你只需写一次测试，然后任意运行而没有额外的开销。

良好的测试覆盖率自有其好处，此外，测试也可以驱动软件的设计，即测试驱动设计，即在实际编码实现之前编写测试用例。一开始,你编写一个非常简单的注定失败的测试用例（因为被测试的代码还不存在），然后编写必要的实现代码，直到测试通过。一旦出现这种情况，您将扩展测试用例以覆盖更多的需求并再次编码实现。通过重复这些步骤，生成的代码看起来通常会非常不同于一开始就直接编码实现而得到代码。

在JavaScript中的单元测试与其它编程语言中没有太大的不同。你需要一个提供TestRunner的小框架，以及一些工具来编写实际的测试。

#自动单元测试
##问题
您想自动测试您的应用程序和框架，甚至试图使用测试驱动设计。编写自己的测试框架很诱人，但在各种浏览器中测试JavaScript代码需要很多的工作来兼顾所有的细节和特殊的要求。

##解决方案
好吧，虽然有其他的JavaScript单元测试框架，你已经决定要看看QUnit。QUnit是jQuery的单元测试框架，用于各种各样的项目。

要使用QUnit，你只需要在你的HTML页面引入两个QUnit文件。QUnit由两部分组成，qunit.js:TestRunner和测试框架，qunit.css:测试套件结果页的样式。

```javascript
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>QUnit basic example</title>
  <link rel="stylesheet" href="/resources/qunit.css">
</head>
<body>
  <div id="qunit"></div>
  <div id="qunit-fixture"></div>
  <script src="/resources/qunit.js"></script>
  <script>
    test( "a basic test example", function() {
      var value = "hello";
      equal( value, "hello", "We expect value to be hello" );
    });
  </script>
</body>
</html>
```

在浏览器中打开此文件，结果如下图所示:
![alt text](https://raw.github.com/cssrain/qunitcookbook/master/src/qunit_1.png "测试结果1")

在`<body>`元素中唯一必要的标记是一个`ID ="qunit-fixture"`的`<div>` 。所有QUnit测试都需要它，即使它本身是空的。它提供了测试用的fixture，在“保持测试原子化”一节中会详细说明。

有趣的的部分是跟在`qunit.js`后面的`<script>`元素。它包含一个`test`函数调用，带有两个参数：一个字符串和一个函数。字符串表示测试的名称，稍后会用来显示测试结果。函数包含了实际的测试代码，包含一个或多个断言。该示例使用两个断言:`ok()`和`equal()` ，在“断言结果”一节中会详细说明。

请注意，没有`document-ready`块。TestRunner负责处理那些：调用`test()`只是把测试用例添加到队列中，其执行将被延迟并由TestRunner和控制。

## 讨论
测试套件的页眉显示如下几个部分：页面的标题，测试状态条（绿色表示全通过，红色条表示至少有一个测试失败），若干复选框用于过滤测试结果，一个蓝色条显示浏览器信息（方便对不同的浏览器测试结果截图）。

“Hide passed tests”复选框在运行大量的测试时很有用。选中该复选框，将隐藏一切通过的测试用例，只显示失败的测试（参见下面的高效开发小节）。

“Check for Globals”复选框让QUnit在每个测试之前和之后，检查全局变量差异。如果有变量被添加或删除，会导致测试失败并列出差异。这将有助于确保你的测试代码与被测代码免于意外导出任何全局变量。

“No try-catch”复选框让QUnit不捕获异常。这样你可以得到一个“原生”的异常，这对那些难以调试的破烂浏览器有极大帮助，没错，就是在说你，IE6。

页眉下面是一个总结，显示测试的总时间，全部断言和失败断言的数量。测试仍在运行时，它会显示哪个测试用例正在执行。

页面的主体是测试结果。个条目开头是测试用例的名称，其后的括号里数字分别代表失败，通过，和总的断言数量。点击条目显示断言的结果，通常包括预期值和实际值。最后的“Rerun”链接用来单独运行该测试用例。


#断言结果
##问题
任何单元测试的本质元素是断言（笔者注：断言的白话意思是提前确定）。测试作者需要让单元测试框架拿期望值与实际值进行比较。

##解决方案
QUnit提供了三种断言。

###ok(truthy[,message])
最基本的一条是`ok()` ，只需要一个参数。如果参数的计算结果为true，则断言通过，否则断言失败。此外，它接受一个字符串用于显示测试结果：
```javascript
test( "ok test", function() {
  ok( true, "true succeeds" );
  ok( "non-empty", "non-empty string succeeds" );

  ok( false, "false fails" );
  ok( 0, "0 fails" );
  ok( NaN, "NaN fails" );
  ok( "", "empty string fails" );
  ok( null, "null fails" );
  ok( undefined, "undefined fails" );
});
```

###equal( actual, expected [, message ] )
`equal`断言采用简单的比较操作符`（==）`比较实际值和期望值。当它们相等时，则断言通过，否则断言失败。失败时，除了以一个给定的消息之外，实际值和期望值也被显示在测试结果中：
```javascript
test( "equal test", function() {
  equal( 0, 0, "Zero; equal succeeds" );
  equal( "", 0, "Empty, Zero; equal succeeds" );
  equal( "", "", "Empty, Empty; equal succeeds" );
  equal( 0, 0, "Zero, Zero; equal succeeds" );

  equal( "three", 3, "Three, 3; equal fails" );
  equal( null, false, "null, false; equal fails" );
});
```
相比`ok()` ，`equal()`使得容易调试失败的测试，因为它显式给出了造成测试失败的值。

如果你需要一个严格的比较`(===)`，使用`strictEqual()`。

###deepEqual( actual, expected [, message ] )
`deepEqual()`断言可以像`equal()`那样使用，但它适用的场景更多。它采用了更精确的比较操作符`（===）`而不是简单的`（==）`。这样一来，` undefined`不等于`null`，`0`，或空字符串（`""`）。它也可以比较对象的内容，使`{key:value}`等于`{key:value}`，即使两个对象是不同的实例。

`deepEqual()`也可以处理NaN，日期，正则表达式，数组和函数，而`equal()`只是检查对象的实例：
```javascript
test( "deepEqual test", function() {
  var obj = { foo: "bar" };
  deepEqual( obj, { foo: "bar" }, "Two objects can be the same in value" );
});
```
在一般情况下，deepEqual()是更好的选择。如果你明确地不想比较两个值的内容，则仍然可以使用equal()。

##同步回调
###问题
有时候，在你的代码环境可能会阻止断言执行，导致测试静默失败。

###解决方案
QUnit提供了一个特殊的断言，用来定义一个测试用例中断言的总数。当测试完成后没有正确数量的断言就会失败，不管其他断言(如果有的话)的结果如何。

用法简单直白，只需要在测试开始时调用`expect()`，唯一的参数是期望的断言总数：
```
test( "a test", function() {
  expect( 2 );

  function calc( x, operation ) {
    return operation( x );
  }

  var result = calc( 2, function( x ) {
    ok( true, "calc() calls operation function" );
    return x * x;
  });

  equal( result, 4, "2 square equals 4" );
});
```
另外，期望的断言总数可以作为第二个参数传递给`test()`：
```
test( "a test", 2, function() {

  function calc( x, operation ) {
    return operation( x );
  }

  var result = calc( 2, function( x ) {
    ok( true, "calc() calls operation function" );
    return x * x;
  });

  equal( result, 4, "2 square equals 4" );
});
```
实例：
```
test( "a test", 1, function() {
  var $body = $( "body" );

  $body.on( "click", function() {
    ok( true, "body was clicked!" );
  });

  $body.trigger( "click" );
});
```

###讨论
`expect()`只在测试回调时最有用。当所有的断言都运行在`test`函数的作用域内时， `expect()`没有额外的作用——防止断言运行的任何错误都会导致测试最终失败，因为TestRunner会捕获错误并让单元测试失败。

##异步回调
###问题
虽然`expect()`在测试同步回调（见“同步回调”小节）很有用，但异步回调时就怂了。异步回调与TestRunner队列和执行测试的方式有冲突。当test内的代码的发起了一个timeout或interval或Ajax请求，TestRunner将继续运行剩余的代码，以及之后的测试用例，而不是等待异步操作的结果。

###解决方案
用`asyncTest()`代替`test()`来包装你的断言，并在该测试用例执行完成并准备好继续时调用`start()`：
```
asyncTest( "asynchronous test: one second later!", function() {
  expect( 1 );

  setTimeout(function() {
    ok( true, "Passed and ready to resume!" );
    start();
  }, 1000);
});
```
实际的例子：
```
asyncTest( "asynchronous test: video ready to play", 1, function() {
  var $video = $( "video" );

  $video.on( "canplaythrough", function() {
    ok( true, "video has loaded and is ready to play" );
    start();
  });
});
```
##测试用户操作
###问题
依赖于用户动作的代码，不能简单地调用一个函数来测试。通常，元素事件会绑定匿名函数，如click，这些需要模拟。

###解决方案
您可以使用jQuery的`trigger()`方法来触发事件并测试预期行为。如果不希望原生的浏览器事件被触发，可以用`triggerHandler()`只执行绑定的事件处理程序。这对测试链接的click事件非常有用，因为`trigger()`会导致浏览器跳转，在测试时我们几乎不会期望这种行为。

假设我们有一个简单的键盘记录器要测试：
```
function KeyLogger( target ) {
  if ( !(this instanceof KeyLogger) ) {
    return new KeyLogger( target );
  }
  this.target = target;
  this.log = [];

  var self = this;

  this.target.off( "keydown" ).on( "keydown", function( event ) {
    self.log.push( event.keyCode );
  });
}
```
我们可以手动触发按键事件来观察记录器是否正常工作：
```
test( "keylogger api behavior", function() {

  var event,
      $doc = $( document ),
      keys = KeyLogger( $doc );

  // trigger event
  event = $.Event( "keydown" );
  event.keyCode = 9;
  $doc.trigger( event );

  // verify expected behavior
  equal( keys.log.length, 1, "a key was logged" );
  equal( keys.log[ 0 ], 9, "correct key was logged" );

});
```

###讨论
如果你的事件处理程序不依赖于任何特定的事件属性，你可以简单地调用`trigger(eventType)`。否则你需要使用`$.Event`来创建一个事件对象，并设置必要的属性，如前面所示。

同样重要的，是为复杂的行为触发所有相关的事件，如dragging，它由mousedown，至少一个mousemove，和mouseup事件构成。请记住，即使是一些看似简单的事件，实际上也是复合的，例如，一个click实际上是一个mousedown，mouseup，然后才是click。是否真的需要触发所有这三个事件取决于被测代码。大多数情况下只触发click就行了。

如果那还不够，你有几个可选框架，能帮助模拟用户事件：

[syn](https://github.com/bitovi/syn) “是一个人工合成的事件库，几乎可以像一个真实的用户的行为一样处理typing，clicking，moving和dragging。” 基于QUnit的FuncUnit在使用它，来对Web应用程序做功能测试。

[JSRobot](http://tinymce.ephox.com/jsrobot) “一个Web的应用程序测试工具，可以产生真正的按键，而不是简单地模拟了JavaScript事件触发，这使得按键能触发浏览器实际的行为，别的框架做不到这点。”

[DOH Robot](http://dojotoolkit.org/reference-guide/1.8/util/dohrobot.html) “提供了一个API，使测试人员能使用真实的，跨平台的，系统级的输入事件来自动化UI测试”。这可以让你非常接近“真正的”浏览器事件，只是需要使用Java applets。

[keyvent.js](https://github.com/gtramontina/keyvent.js) - "键盘事件模拟器."

