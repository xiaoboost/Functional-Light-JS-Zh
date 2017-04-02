第三章：管理函数入口
---

在第二章的“函数输入”这一节中，我们讨论了函数形参和实参的基础。我们还看到了一些语法上的技巧，来缓解它们使用上的一些问题，比如`...`运算符和解构赋值。

在当时的讨论中，我建议尽可能的尝试设计只有一个参数的函数。但事实上要做到这一点很不容易，你并不会总是控制你需要使用的函数签名。

现在，我们将会把我们的目光转向更为复杂更为强大的模式。用于在这些场景中竞争函数输入。

# 一些在现在，一些在未来
如果一个函数拥有多个实参，你可能马上就需要使用一些参数，同时其余的参数将会被留下来稍后再使用。

思考这个函数：
```JavaScript
function ajax(url,data,callback) {
    // ..
}
```

让我们想象一下吧，你现在要设置几个API的调用，其中URL是预先就知道的，但是处理响应额数据和回调要等会儿才会知道。

当然，你也可以等到当所有的数据都已经知道的时候再调用`ajax(..)`，此时再去引用全局的常量*URL*。但是还有另外一种方法，那就是创建一个已经带有`url`实参的函数引用。

我们要做的是创建一个仍然在底层调用`ajax(..)`的新函数，并且手动将*API URL*设置为第一个实参，然后等待接受另外两个参数。
```JavaScript
function getPerson(data,cb) {
    ajax( "http://some.api/person", data, cb );
}

function getOrder(data,cb) {
    ajax( "http://some.api/order", data, cb );
}
```

手动指定这些函数调用的封装当然是可以的，但它会变得非常冗长，特别是在有不同参数预设的变化时，比如：
```JavaScript
function getCurrentUser(cb) {
    getPerson( { user: CURRENT_USER_ID }, cb );
}
```
在实践中，函数式的编程者们总是习惯于寻找经常重复运用的操作模式，并尝试把这些行为转变为通用的可复用的实用程序。事实上，我相信这已经是许多读者的本能了。所以这并不是只有在函数式编程中才会出现的事情，但是这毫无疑问对于函数式来说非常重要。

为了构思上述这种用于参数预置的实用程序，我们不能仅看上面手动的实现，还需要从概念上来审视它，看看到底发生了什么。

// TODO 这一段存疑
我们一般会这么描述这种模式，`getOrder(data,cb)`函数是`ajax(url,data,cb)`函数的*局部应用(partial application)*。这个术语就是指，输入的实参被*应用于*函数调用时的形参。正如你所看到的，我们只使用了前面的一些参数——特别给`url`形参提供了实参——而剩下的则会在稍后被应用。

对于这种模式稍微正式点的描述是这样的，局部应用能够严格的降低函数的计数值；计数值，是指函数预期形参的输入数量。我们的计数值从原始函数`ajax(..)`的`3`降低到了`getOrder(..)`函数的`2`。

我们再来定义一个实用工具函数`partial(..)`：
```JavaScript
function partial(fn,...presetArgs) {
	return function partiallyApplied(...laterArgs){
		return fn( ...presetArgs, ...laterArgs );
	};
}
```
Tip：上面这个片段可不要看过就完了，稍微花点时间来消化这个程序到底发生了什么，以确保你真的了解了它。这里的这个代码模式实际上会在本书的其它部分一遍又一遍的出现，所以在现在就马上掌握它吧！

`partial(..)`函数接受到了一个局部应用的函数`fn`。然后传入的任何后续实参都会被聚合到`presetArgs`数组中，以备后续使用。

这个函数创建并返回了一个新的内部函数（为了清楚起见，我们称之为`partiallyApplied(..)`），其自身的实参被聚合到了名为`laterArgs`的数组中。

注意到这个内部函数对`fn`和`presetArgs`的引用了吗？这部分是如何工作的呢？在`partial(..)`运行之后，内部函数是如何能够保持对`fn`和`presetArgs`的访问的呢？如果你的答案是*闭包*，恭喜！你答对了！内部函数`partiallyApplied(..)`闭合了`fn`和`presetArgs`变量，所以无论它在哪里运行，它都可以随时访问这两个变量。看到了吗，理解闭包实在是太重要了。

当`partiallyApplied(..)`函数在你的程序的其他地方运行的时候，它将调用闭包中的`fn`来运行原始的函数，拿出一开始局部应用中输入的实参`presetArgs`（在闭包中），然后再使用之后输入的`laterArgs`实参。

如果你觉得有点晕，请停下来重新阅读这里。相信我，在接下来的文本中你会很高兴现在的你这么做了。

作为附注，函数式编程者们通常会喜欢这种代码较短的`=>`箭头函数语法（请参考第一章 “语法”），例如：
```JavaScript
var partial =
	(fn, ...presetArgs) =>
		(...laterArgs) =>
			fn( ...presetArgs, ...laterArgs );
```
这么写是没有问题的，而且毫无疑问要更简洁更稀疏。但是我个人觉得无论这里的数学符号再怎么对称，它在整体可读性方面失去的更多，所有这些函数都是匿名的，并且由于这里模糊的函数边界，导致想要辨别这里的闭包变得更加困难。

无论使用哪种语法，你都能看出，我们使用`partial(..)`函数实现了提前局部应用的函数：
```JavaScript
var getPerson = partial( ajax, "http://some.api/person" );

var getOrder = partial( ajax, "http://some.api/order" );
```
在这里暂停一下，然后好好想想`getPerson(..)`函数的形态/内部。它应看起来应该是这样：
```JavaScript
var getPerson = function partiallyApplied(...laterArgs) {
	return ajax( "http://some.api/person", ...laterArgs );
};
```
`getOrder(..)`也是如此，那么`getCurrentUser(..)`又是怎样的呢？
```JavaScript
// version 1
var getCurrentUser = partial(
	ajax,
	"http://some.api/person",
	{ user: CURRENT_USER_ID }
);

// version 2
var getCurrentUser = partial( getPerson, { user: CURRENT_USER_ID } );
```
我们可以直接指定`url`和数据的实参来定义`getCurrentUser(..)`（版本一），也可以把` getCurrentUser(..)`定义为`getPerson(..)`的局部应用，同时仅指定附加的数据实参。

版本二是个更为简洁的表达，因为它重用了一些已经定义的东西，因此我认为它更为符合FP的精神。

为了确保我们理解了这两个版本的函数是如何工作的，下面分别是它们此时的完整代码：
```JavaScript
// version 1
var getCurrentUser = function partiallyApplied(...laterArgs) {
	return ajax(
		"http://some.api/person",
		{ user: CURRENT_USER_ID },
		...laterArgs
	);
};

// version 2
var getCurrentUser = function outerPartiallyApplied(...outerLaterArgs) {
	var getPerson = function innerPartiallyApplied(...innerLaterArgs){
		return ajax( "http://some.api/person", ...innerLaterArgs );
	};

	return getPerson( { user: CURRENT_USER_ID }, ...outerLaterArgs );
}
```
同样的，在这里暂停，并重新阅读这里的代码片段，以确保你理解了这里发生了什么。

Note: 第二个版本有一个额外的函数包装层，这看起来可能有点奇怪以及多余，但是这也是你在函数式编程中必须要习惯的事情。随着文章的进行，我们将会把许多函数不断堆叠在一起。记住，这是*函数式编程*！

我们来看看局部应用有用性的另一个例子。考虑这样一个`add(..)`函数，它接受两个实参，并把它们加了起来：
```JavaScript
function add(x,y) {
	return x + y;
}
```
现在想象一下，这里有一个数字列表，我们想要给这个列表中每个数字都加上一个数字。我们将使用JS数组中内置的`map(..)`方法。
```JavaScript
[1,2,3,4,5].map( function adder(val){
	return add( 3, val );
} );
// [4,5,6,7,8]
```
Note: 不要担心你之前从没见过`map(..)`函数，我们将会在本书的后面对它进行更为详细的介绍。现在你只需要知道它将枚举一个数组的所有元素，并通过调用一个函数来产生新的值，这些新的值将会组成一个新的数组。

我们无法直接传递`add(..)`给`map(..)`是因为`add(..)`的签名与`map(..)`函数的映射并不匹配。此时局部应用就能够帮助我们了：我们可以把`add(..)`函数的签名改写成可以匹配的东西。
```JavaScript
[1,2,3,4,5].map( partial( add, 3 ) );
// [4,5,6,7,8]
```

## `bind(..)`
JavaScript有一个名为`bind(..)`的内建方法，它对所有的函数都有效。它有两个能力：预设`this`上下文并应用部分参数。

我认为将这两个功能合并在一个方法中是非常不幸的。有时候你会想要显式的绑定`this`上下文，而不是部分的应用参数。有时候你又会想要应用部分参数，但并不关心`this`绑定。我个人几乎从来没有碰到这两者同时进行的情景。

后一种情况更是尴尬，因为你必须传递一个可忽略的占位符，这个绑定的实参（第一个）通常是`null`。

像是这样：
```JavaScript
var getPerson = ajax.bind( null, "http://some.api/person" );
```
这个`null`真的让我发狂。

## 反转实参
回想一下，我们的`Ajax`函数的签名是`ajax( url, data, cb )`。如果我们想先局部应用`cb`但是又想等会再应用`data`和`url`？我们可以创建这样一个方法，这个方法将会把原函数包装起来，并且反转其参数顺序：
```JavaScript
function reverseArgs(fn) {
	return function argsReversed(...args){
		return fn( ...args.reverse() );
	};
}

// or the ES6 => arrow form
var reverseArgs =
	fn =>
		(...args) =>
			fn( ...args.reverse() );
```
现在我们可以反转`ajax(..)`实参的顺序了，这样我们从右边的参数开始局部应用，而不是从左边开始。想要恢复预期的顺序，我们可以继续反转局部应用的函数：
```JavaScript
var cache = {};

var cacheResult = reverseArgs(
	partial( reverseArgs( ajax ), function onResult(obj){
		cache[obj.id] = obj;
	} )
);

// later:
cacheResult( "http://some.api/person", { user: CURRENT_USER_ID } );
```

现在，我们可以使用相同的反转局部应用的技巧，来定义一个`partialRight(..)`方法，它将从右边开始局部应用：
```JavaScript
function partialRight( fn, ...presetArgs ) {
	return reverseArgs(
		partial( reverseArgs( fn ), ...presetArgs.reverse() )
	);
}

var cacheResult = partialRight( ajax, function onResult(obj){
	cache[obj.id] = obj;
});

// later:
cacheResult( "http://some.api/person", { user: CURRENT_USER_ID } );
```

`partialRight(..)`这种实现不能保证特定的形参接收到特定的局部应用的值，它只能确保局部应用的右半部分值是传递给原始函数最右边的实参。

比如：
```JavaScript
function foo(x,y,z) {
	var rest = [].slice.call( arguments, 3 );
	console.log( x, y, z, rest );
}

var f = partialRight( foo, "z:last" );

f( 1, 2 );			// 1 2 "z:last" []

f( 1 );				// 1 "z:last" undefined []

f( 1, 2, 3 );		// 1 2 3 ["z:last"]

f( 1, 2, 3, 4 );	// 1 2 3 [4,"z:last"]
```
`"z:last"`这个值被确实应用到`z`这个形参中，只有当`f(..)`函数时恰好只传递了两个实参的情况（匹配`x`和`y`形参）。在其余情况下，不管你在前面传入多少个实参，`"z:last"`都将仅仅匹配最右边的实参。

# 一次一个
我们来看一个类似于局部应用的技术，一个期望输入多个实参的函数被分解成连续的链式函数，每个函数都将只接收一个实参（计数值：1），并且将会返回另一个函数来接受下一个实参。

这个技术被称之为*柯里化 currying*。

首先，我们先来想想之前已经创建好的`ajax(..)`函数被柯里化之后的样子吧。按照定义，我们应该这么使用它：
```JavaScript
curriedAjax( "http://some.api/person" )
	( { user: CURRENT_USER_ID } )
		( function foundUser(user){ /* .. */ } );
```
也许把它拆成三个独立的调用有助于我们更好的理解情况：
```JavaScript
var personFetcher = curriedAjax( "http://some.api/person" );

var getCurrentUser = personFetcher( { user: CURRENT_USER_ID } );

getCurrentUser( function foundUser(user){ /* .. */ } );
```
在这里既没有立刻使用所有参数（比如`ajax(..)`），也没有先应用部分然后再应用剩下的（比如`partial(..)`），这里的`curriedAjax(..)`函数在每个独立的函数调用中都只接受一个实参。

柯里化和局部应用在某种意义上是比较类似的，因为每个连续的柯里调用都可以看作是把另一个实参局部应用到原始函数中，直到所有实参都被传递了进去。

它们之间的最主要的区别是，`curriedAjax(..)`将会显式的返回一个函数（我们叫它`curriedGetPerson(..)`），它需要*仅输入下一个*实参数据，而不是所有剩下的实参（就像之前的`getPerson(..)`）。

假如原始函数预期输入5个实参，那么该函数的柯里形式只需要第一个实参，然后返回一个函数来接受第二个实参，这个函数只需要接受第二个实参，并返回一个函数来接受第三个实参……以此类推。

所以，柯里化将多计数值的函数转化为一个系列函数的链式调用。

我们如何定义一个方法来实现柯里化呢？我们将会使用第二章中的一些技巧：
```JavaScript
function curry(fn,arity = fn.length) {
	return (function nextCurried(prevArgs){
		return function curried(nextArg){
			var args = prevArgs.concat( [nextArg] );

			if (args.length >= arity) {
				return fn( ...args );
			}
			else {
				return nextCurried( args );
			}
		};
	})( [] );
}
```
给ES6`=>`符号的粉丝们：
```JavaScript
var curry =
	(fn, arity = fn.length, nextCurried) =>
		(nextCurried = prevArgs =>
			nextArg => {
				var args = prevArgs.concat( [nextArg] );

				if (args.length >= arity) {
					return fn( ...args );
				}
				else {
					return nextCurried( args );
				}
			}
		)( [] );
```
这个方法将会从实参集合`prevArgs`为空`[]`数组的时候开始，并将每个接收到的`nextArg`添加其中，然后调用串联好的`args`数组。当`args.length`小于`arity`（原始函数`fn(..)`声明/期望的的形参数量）时，将会返回另一个`curried(..)`函数来继续收集接下来的nextArg`实参，传递运行的`args`集合作为`prevArgs`。一旦我们有了足够的实参，就可以用它们来执行原始函数`fn(..)`函数了。

默认情况下，这样的实现依赖于能够检查待柯里化函数的`length`属性，以确定在收集所有预期的实参之前需要迭代多少次柯里化。

如果你对有不准确`length`属性的函数使用了这里的`curry(..)`实现——如何函数的形参签名包含了默认形参值，形参解构赋值，又或者是`...args`运算，请参考第二章——你需要手动的将`arity`（`curry(..)`的第二个形参）传递进去，以确保`curry(..)`的正确工作。

这里是我们如何使用`curry(..)`来改写我们之前的`ajax(..)`的例子：
```JavaScript
var curriedAjax = curry( ajax );

var personFetcher = curriedAjax( "http://some.api/person" );

var getCurrentUser = personFetcher( { user: CURRENT_USER_ID } );

getCurrentUser( function foundUser(user){ /* .. */ } );
```
每次调用都给原始函数`ajax(..)`的调用增加一个实参，直到提供了所有的三个参数，此时`ajax(..)`就被执行了。

还记得我们之前给列表中每个值加`3`的例子吗？我们之前说过，柯里化和局部应用是很相似的，所以我们可以用几乎相同的方法来执行这个任务：
```JavaScript
[1,2,3,4,5].map( curry( add )( 3 ) );
// [4,5,6,7,8]
```
这两者之间有什么区别？`partial(add,3)` vs `curry(add)(3)`。为什么你会选择`curry(..)`而不是`partial(..)`？在你提前知道`add(..)`是用来调整的函数，但此时`3`这个值你好并不知道：
```JavaScript
var adder = curry( add );

// later
[1,2,3,4,5].map( adder( 3 ) );
// [4,5,6,7,8]
```
另一个数字的例子会是怎么样呢，把它们同时排列出来就行啦：
```JavaScript
function sum(...args) {
	var sum = 0;
	for (let i = 0; i < args.length; i++) {
		sum += args[i];
	}
	return sum;
}

sum( 1, 2, 3, 4, 5 );						// 15

// now with currying:
// (5 to indicate how many we should wait for)
var curriedSum = curry( sum, 5 );

curriedSum( 1 )( 2 )( 3 )( 4 )( 5 );		// 15
```
在这里柯里化的好处是，每次调用传递实参都会产生另一个更为专业的函数，我们可以在程序中捕获并使用该新函数。局部应用则是先指定部分实参，然后生成一个等待其余实参的函数。

如果要使用局部应用来实现依次指定一个参数，则必须在每个连续的函数上持续调用`partialApply(..)`。柯里化则能自动进行，这样一次一个的独立参数调用更加符合人体工程学。

在JavaScript中，柯里化和局部应用都是用了闭包来记录实参，直到所有实参都被接收到了，然后就能执行原始运算了。


现：17324字符  
共：46016字符  
进度： 37%  
