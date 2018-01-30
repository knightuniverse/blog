---
title: JavaScript异常和调用堆栈跟踪
date: 2017-12-05 08:26:12
tags: 技术
---

原文
--

[JavaScript Errors and Stack Traces in Depth](<http://lucasfcosta.com/2017/02/17/JavaScript-Errors-and-Stack-Traces.html>)

调用栈Call Stack是如何工作的
-------------------

栈是一个后进先出LIFO （Last in，First out）的数据结构。调用堆栈实际上就是一个方法列表，按调用顺序保存所有在运行期被调用的方法。**调用堆栈会将当前正在执行的函数调用压入堆栈，一旦函数调用结束，又会将它移出堆栈。**

用```console.trace()```编写一个简单的例子来演示一下

	function c() {
		console.log('c');
		console.trace();
	}
	function b() {
		console.log('b');
		c();
	}
	function a() {
		console.log('a');
		b();
	}
	a();


我们可以看到```console```输出的结果

	console.trace
	c @ VM59:3
	b @ VM59:7
	a @ VM59:11
	(anonymous) @ VM59:13


我们调用```console.trace()```是在```c```方法里，这个时候```c```还在执行，并没有返回，因此从```console```就能看到调用堆栈顶就是```c```。我们可以稍微改变一下，比如把```console.trace()```放在```b```方法里调用，如下：

	function c() {
		console.log('c');
	}
	function b() {
		console.log('b');
		c();
		console.trace();
	}
	function a() {
		console.log('a');
		b();
	}
	a();


这时候我们再观察```console```，就看不到```c```方法了

	VM61:8 console.trace
	b @ VM61:8
	a @ VM61:13
	(anonymous) @ VM61:16


因为```console.trace()```的调用是发生在了```c```调用之后，因此这个时候，栈顶```c```的帧已经出栈，自然就看不到了。

Error对象以及异常处理
-------------

通常程序有异常的时候都会有一个```Error```对象抛出。```Error.prototype```有以下几种标准属性：


* ```constructor```
* ```message```
* ```name```


更多的，可以翻翻[MDN的文档](<https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error/prototype)%E3%80%82%E5%85%B6%E4%B8%AD%E6%9C%89%E4%B8%80%E4%B8%AA```stack```%E5%B1%9E%E6%80%A7%EF%BC%8C%E8%A6%81%E9%87%8D%E7%82%B9%E5%85%B3%E6%B3%A8%E3%80%82%E5%B0%BD%E7%AE%A1%E8%BF%99%E6%98%AF%E4%B8%80%E4%B8%AA%E9%9D%9E%E6%A0%87%E5%87%86%E5%B1%9E%E6%80%A7%EF%BC%8C%E4%BD%86%E6%98%AF%E7%BB%9D%E5%A4%A7%E5%A4%9A%E6%95%B0%E6%B5%8F%E8%A7%88%E5%99%A8%E9%83%BD%E6%94%AF%E6%8C%81%E8%BF%99%E4%B8%AA%E5%B1%9E%E6%80%A7>。

一般我们使用```try/catch```来捕获异常，同时，我们还可以使用```finally```来做一些清理的工作，因为```finally```里的代码是一定会执行的。

	try {
		console.log('The try block is running...');
	} finally {
		try {
			throw new Error('Error inside finally.');
		} catch (err) {
			console.log('Caught an error inside the finally block.');
		}
	}


有一个值得探讨的地方，那就是，**你可以throw任何数据而不仅仅是一个Error类的实例**。

	function runWithoutThrowing(func) {
		try {
			func();
		} catch (e) {
			console.log('There was an error, but I will not throw it.');
			console.log('The error\'s message was: ' + e.message)
		}
	}
	function funcThatThrowsString() {
		throw 'I am a String.';
	}
	runWithoutThrowing(funcThatThrowsString);


这种情况下，```e.message```的值一定就是```undefined```了，因为你抛出的并不是一个Error类的实例。

异常还可以作为第一个参数传给callback函数。举个```fs.readdir```的例子，

	const fs = require('fs');
	fs.readdir('/example/i-do-not-exist', function callback(err, dirs) {
		if (err instanceof Error) {
			// `readdir` will throw an error because that directory does not exist
			// We will now be able to use the error object passed by it in our callback function
			console.log('Error Message: ' + err.message);
			console.log('See? We can use Errors without using try statements.');
		} else {
			console.log(dirs);
		}
	});


处理调用堆栈
------

思路就两种：


1. [Error.captureStackTrace（NodeJS）](<https://nodejs.org/api/errors.html#errors_error_capturestacktrace_targetobject_constructoropt>)
2. Error.prototype.stack


Error.captureStackTrace是NodeJS提供的一个方法，这个方法会捕捉当前的调用堆栈，然后保存到你指定的对象。

	const myObj = {};
	function c() {
	}
	function b() {
		// Here we will store the current stack trace into myObj
		Error.captureStackTrace(myObj);
		c();
	}
	function a() {
		b();
	}
	// First we will call these functions
	a();
	// Now let's see what is the stack trace stored into myObj.stack
	console.log(myObj.stack);


另外一种就是利用Error对象的```stack```属性。但里有个问题，就是你不知道在```try/catch```里抛出的是什么样的值，这个值它不一定是Error类的实例。不过我们依然能够处理，而且是非常巧妙的进行处理。比如看看[Chai](<http://chaijs.com/)%E8%BF%99%E4%B8%AA%E6%96%AD%E8%A8%80%E5%BA%93%E7%9A%84```AssertionError```%E7%B1%BB%E7%9A%84%E6%9E%84%E9%80%A0%E5%87%BD%E6%95%B0>。

	// `ssfi` stands for "start stack function". It is the reference to the
	// starting point for removing irrelevant frames from the stack trace
	function AssertionError (message, _props, ssf) {
	  var extend = exclude('name', 'message', 'stack', 'constructor', 'toJSON')
		, props = extend(_props || {});
	  // Default values
	  this.message = message || 'Unspecified AssertionError';
	  this.showDiff = false;
	  // Copy from properties
	  for (var key in props) {
		this[key] = props[key];
	  }
	  // Here is what is relevant for us:
	  // If a start stack function was provided we capture the current stack trace and pass
	  // it to the `captureStackTrace` function so we can remove frames that come after it
	  ssf = ssf || arguments.callee;
	  if (ssf && Error.captureStackTrace) {
		Error.captureStackTrace(this, ssf);
	  } else {
		// If no start stack function was provided we just use the original stack property
		try {
		  throw new Error();
		} catch(e) {
		  this.stack = e.stack;
		}
	  }
	}


参考文档
----


1. [arguments.callee](<https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments/callee>)
2. [arguments.caller](<https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments/caller>)
3. [Error](<https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error>)

