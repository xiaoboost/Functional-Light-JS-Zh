第三章：管理函数入口
---

在第二章的“函数输入”这一节中，我们讨论了函数形参和实参的基础。我们还看到了一些语法上的技巧，来缓解它们使用上的一些问题，比如`...`运算符和解构赋值。

在当时的讨论中，我建议尽可能的尝试设计只有一个参数的函数。但事实上要做到这一点很不容易，你并不会总是控制你需要使用的函数签名。

现在，我们将会把我们的目光转向更为复杂更为强大的模式。用于在这些场景中竞争函数输入。

# Some Now, Some Later

如果一个函数拥有多个实参，你可能马上就需要使用一些参数，同时其余的参数将会被留下来稍后再使用。

思考这个函数：
```JavaScript
function ajax(url,data,callback) {
    // ..
}
```

让我们想象一下吧，你现在要设置几个API的调用，其中URL是预先就知道的，但是处理响应额数据和回调要等会儿才会知道。

当然，你也可以等到当所有的数据都已经知道的时候再调用`ajax(..)`，此时再去引用全局的常量*URL*。但是还有另外一种方法，那就是创建一个已经带有`url`实参的函数引用。

我们要做的是创建一个仍然在底层调用`ajax(..)`的新函数，并且手动将你关注的*API URL*设置为第一个实参，然后等待接受另外两个参数。
```JavaScript
function getPerson(data,cb) {
    ajax( "http://some.api/person", data, cb );
}

function getOrder(data,cb) {
    ajax( "http://some.api/order", data, cb );
}
```

手动指定这些函数调用的封装当然是可以的，但它会变得非常乏味，特别是在有不同参数预设的变化时，比如：
```JavaScript
function getCurrentUser(cb) {
    getPerson( { user: CURRENT_USER_ID }, cb );
}
```