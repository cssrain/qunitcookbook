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


