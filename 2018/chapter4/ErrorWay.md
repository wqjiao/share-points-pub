# 前端错误收集以及统一异常处理

提前对错误有所准备，将错误捕获做出反应，给用户更好的体验

也可以通过对错误信息的收集和分析，主动的去发现一些潜藏着的代码问题。

## 客户端收集

### window.onerror 会全局的在JavaScript运行时错误、语法错误发生时触发。
	```
	window.onerror = (msg, url, lineNum, colNum, err) => {
	console.log(`错误发生的异常信息（字符串）:${msg}`)
	console.log(`错误发生的脚本URL（字符串）:${url}`)
	console.log(`错误发生的行号（数字）:${lineNum}`)
	console.log(`错误发生的列号（数字）:${colNum}`)
	console.log(`错误发生的Error对象（错误对象）:${err}`)
	};
	```

### 注意：
		
	这里我们可以拿到的是被throw出来，没有被catch过的错误。而不能拿到promise这样的错误。

	这种方式只能拿到一个Script error并没有错误本身的message、url等信息，在lineNum和colNum也都是0，并不是真正错误发生时的错误信息。

	原因是浏览器在同源策略限制下所产生的。浏览器出于安全上的考虑，当页面引用的非同域的外部脚本中抛出了异常，
	此时本页面无权限获得这个异常详情， 将输出 Script error 的错误信息。

	在Chrome中有这样的安全机制，他不会将完整的跨域错误信息暴露给你，
	只在chrome中会出现这样的情况，在Firefox，Safari中均可以正常的拿到完整的错误信息。

### 解决Script error

	如果要解决这个问题，可以使用跨源资源共享机制( CORS )

	为页面上script标签添加crossorigin属性。
	```
	<!-- 增加 crossorigin 属性后，浏览器将自动在请求头中添加一个 Origin 字段,告诉服务器自己的来源，服务器再判断是否返回 -->
	<script src="http://xxx.xxx.xxx.x/xxx.js" crossorigin></script>
	```

	响应头中增加 Access-Control-Allow-Origin 来支持跨域资源共享。

## unhandledrejection
	在前文中提到Promise中的错误并不能被try...catch和window.onerror捕获。

	这时候我们就需要unhandledrejection来帮我们捕获这部分错误。
	```
	window.addEventListener('unhandledrejection', (e) => {
	console.log(`Promise.reject()中的内容，告诉你发生错误的原因:${e.reason}`);
	console.log(`Promise对象 :${e.promise}`);
	});
	```

## console.error
	console.error常常被视为打印的日志，可预知的错误，已经被捕获的错误，已经被处理过的内容。所以往往会被忽视不去处理。

	下面的代码很常见,用一个大大的try...catch,将异常捕获然后打一个console.error完事，
	可能对于异常处理这样已经完事，捕获住了错误，没有让程序崩溃，但如果对于错误收集这也是不可缺少的一部分
	```
	try {
		// some code
	} catch (err) {
		console.error(err)
	}
	```
	所以稍稍改造一下console.error，让每一次触发console.error的时候我们可以做一些事情，例如对错误收集系统做一下上报什么的。
	```
	console.error = (func => {
	return str => {
		// 在这里就可以收集到console.error的错误
		// 做一些事情
		func.call(console, str);
	}
	})(console.error);
	```

## 服务端收集
	在Node服务端的收集其实和客户端上大同小异，只是一些方法上的区别.

## uncaughtException
	通过Node的全局处理，捕获所有未被处理的错误,这是最后一层关卡，兜底的操作，如果还不处理的话往往会导致程序崩溃。
	```
	process.on('uncaughtException', err => {
	//do something
	});
	```

## unhandledRejection
	在Node中，Promise中的错误同样不能被 try...catch 和 uncaughtException 捕获。
	
	这时候我们就需要unhandledRejection来帮我们捕获这部分错误。
	```
	process.on('unhandledRejection', err => {
	//do something
	});

	console.error = (func => {
	return str => {
		// 在这里就可以收集到console.error的错误
		// 做一些事情
		func.call(console, str);
	}
	})(console.error);
	```

## 借助框架对异常的处理(以koa为例)
	对于Node端我们往往，可以借助框架对错误进行捕获，像koa就可以通过app.on error对错误在框架这一层进行捕获，

	同样他也是捕获内部没有被catch到的错误，像promise错误并不能捕获。
	```
	app.on('error', (err, ctx) => {
	// do something
	});
	```
	值得一提的是，我们可以在框架内部主动的触发这个error事件，对即使已经被我们捕获了处理过的错误，也继续抛到框架这一层来，方便做很多统一处理。
	```
	ctx.app.emit('error', err, ctx);
	```

## 错误类型的总结
	- 同步错误 => 可以被1.try...catch 2.window.onerror 3.process.on('uncaughtException')捕获。

	- 异步错误 => 例如 setInterval、没有被 await 的异步函数等，是不会被 try...catch 捕获的，
	但是会被 window.onerror和process.on('uncaughtException') 捕获。

	- Promise错误 => Promise.reject(new Error('some wrong'));
	像 promise 错误，是不会被 window.onerror和process.on('uncaughtException') 捕获的，
	更不会被 try...catch 捕获，想要捕获它们只能，process.on('unhandledRejection') 以及
	window.addEventListener('unhandledrejection')

	- 注意：在局部被 try...catch 了的错误是不会继续往上层抛出了的，所以全局处理的捕获是肯定捕获不到的，
	除非在 catch 到以后处理完成，将错误继续向上层 throw。

## 异常的统一处理

	- 整体思路：
		在业务层对错误捕获包装后继续向上层抛出，在包装中的时候，将所有的错误都继承自我们自己定义的错误类，
		在错误类中有很多我们自定义好的错误类型，在抛出的时候只需要简单的抛一下这个错误类型的实例就好，
		在最后中间件的时候我们可以catch到全部的错误做统一的处理。这时的错误是被分过类，分过级的，
		还有一部分可能是之前从未被捕获的，在这就可以干很多事了。

	- 定义错误类
	```
	class SystemError extends Error {
		constructor(message) {
			super(message);
			// 错误类型
			// 错误等级
			// 错误信息
			// ...
		}
		static wrapper(e) {
			const error = new this(e.message);
			// 将e上的各种东西包装到error上
			return error;
		}
	}
	```
	//可以对常见的错误提前定义好
	createDBError(xxx) {
		const sysError = SystemError.wrapper(error);
		// 写入错误信息
		// 写入错误类型
		// 写入错误等级
		// ...
		return sysError;
	}

	//这样在业务中抛错的时候只需要简单的
	throw createDBError(error, { someInfo });

## 错误捕获
	在业务中尽可能精确的捕获错误，根据错误，进行定级，分类等操作，然后继续向上层抛出。

	因为要精确的捕获错误，很容易造成大量try...catch嵌套的的情况，我们要尽可能的避免这样臃肿的代码

	try {
		try {
			// 操作数据库
		} catch (err) {
			throw createDBError(error, { someInfo });
		}
		try {
			// 正常业务
		} catch (err) {
			throw createBusinessError(error, { someInfo });
		}
	} catch (err) {
		throw err
	}

	这时候一定是我们的代码有问题了，这时候我们就要想是不是可以拆分开来，不会造成这样臃肿的局面。

	中间件统一处理
	因为前面所有的错误我们都只做了包装，并且继续上报，所以在最上层的中间件中，我们可以对所有的错误进行统一处理。

	所有经过我们包装的错误都来自于我们自定义的类，我们可以轻易判断哪些错误是我们已知的，哪些是从未捕获到的。
	可以根据错误类型更友好的响应请求和展示页面。
	可以根据错误等级来判断哪些错误只需要收集哪些错误需要报警。
	