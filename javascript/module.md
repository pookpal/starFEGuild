#模块化规范
##CMD 模块定义规范

在 Sea.js 中，所有 JavaScript 模块都遵循 CMD（Common Module Definition） 模块定义规范。该规范明确了模块的基本书写格式和基本交互规则。

在 CMD 规范中，一个模块就是一个文件。代码的书写格式如下：

```javascript
define(factory);
```

####define `Function`

`define` 是一个全局函数，用来定义模块。

#####define `define(factory)`

`define` 接受 `factory` 参数，`factory` 可以是一个函数，也可以是一个对象或字符串。

`factory` 为对象、字符串时，表示模块的接口就是该对象、字符串。比如可以如下定义一个 JSON 数据模块：

```javascript
define({ "foo": "bar" });
```

也可以通过字符串定义模板模块：

```javascript
define('I am a template. My name is {{name}}.');
```

`factory` 为函数时，表示是模块的构造方法。执行该构造方法，可以得到模块向外提供的接口。`factory` 方法在执行时，默认会传入三个参数：`require`、`exports` 和 `module`：

```javascript

define(function(require, exports, module) {

  // 模块代码

});

```
#####define define(id?, deps?, factory)

`define` 也可以接受两个以上参数。字符串 `id` 表示模块标识，数组 `deps` 是模块依赖。比如：

```javascript
define('hello', ['jquery'], function(require, exports, module) {

  // 模块代码

});
```

`id` 和 `deps` 参数可以省略。省略时，可以通过构建工具自动生成。

注意：带 `id` 和 `deps` 参数的 `define` 用法不属于 CMD 规范，而属于 Modules/Transport 规范。

#####define.cmd `Object`

一个空对象，可用来判定当前页面是否有 CMD 模块加载器：

```javascript
if (typeof define === "function" && define.cmd) {
  // 有 Sea.js 等 CMD 模块加载器存在
}
```

####require `Function`

`require` 是 `factory` 函数的第一个参数。

#####require `require(id)`

`require` 是一个方法，接受 模块标识 作为唯一参数，用来获取其他模块提供的接口。

```javascript
define(function(require, exports) {

  // 获取模块 a 的接口
  var a = require('./a');

  // 调用模块 a 的方法
  a.doSomething();

});
```

注意：在开发时，`require` 的书写需要遵循一些 简单约定。

#####require.async `require.async(id, callback?)`

`require.async` 方法用来在模块内部异步加载模块，并在加载完成后执行指定回调。`callback` 参数可选。

```javascript
define(function(require, exports, module) {

  // 异步加载一个模块，在加载完成时，执行回调
  require.async('./b', function(b) {
    b.doSomething();
  });

  // 异步加载多个模块，在加载完成时，执行回调
  require.async(['./c', './d'], function(c, d) {
    c.doSomething();
    d.doSomething();
  });

});
```
注意：`require` 是同步往下执行，`require.async` 则是异步回调执行。`require.async` 一般用来加载可延迟异步加载的模块。

#####require.resolve `require.resolve(id)`

使用模块系统内部的路径解析机制来解析并返回模块路径。该函数不会加载模块，只返回解析后的绝对路径。

```javascript
define(function(require, exports) {

  console.log(require.resolve('./b'));
  // ==> http://example.com/path/to/b.js

});
```
这可以用来获取模块路径，一般用在插件环境或需动态拼接模块路径的场景下。

####exports `Object`

`exports` 是一个对象，用来向外提供模块接口。

```javascript
define(function(require, exports) {

  // 对外提供 foo 属性
  exports.foo = 'bar';

  // 对外提供 doSomething 方法
  exports.doSomething = function() {};

});
```
除了给 `exports` 对象增加成员，还可以使用 `return` 直接向外提供接口。
```javascript
define(function(require) {

  // 通过 return 直接提供接口
  return {
    foo: 'bar',
    doSomething: function() {}
  };

});
```
如果 `return` 语句是模块中的唯一代码，还可简化为：

```javascript
define({
  foo: 'bar',
  doSomething: function() {}
});
```
上面这种格式特别适合定义 JSONP 模块。

特别注意：下面这种写法是错误的！

```javascript
define(function(require, exports) {

  // 错误用法！！!
  exports = {
    foo: 'bar',
    doSomething: function() {}
  };

});
```
正确的写法是用 `return` 或者给 `module.exports` 赋值：

```javascript
define(function(require, exports, module) {

  // 正确写法
  module.exports = {
    foo: 'bar',
    doSomething: function() {}
  };

});
```
提示：`exports` 仅仅是 `module.exports` 的一个引用。在 `factory` 内部给 `exports` 重新赋值时，并不会改变 `module.exports` 的值。因此给 `exports` 赋值是无效的，不能用来更改模块接口。

####module Object

`module` 是一个对象，上面存储了与当前模块相关联的一些属性和方法。

#####module.id `String`

模块的唯一标识。

```javascript
define('id', [], function(require, exports, module) {

  // 模块代码

});
```
上面代码中，`define` 的第一个参数就是模块标识。

#####module.uri String

根据模块系统的路径解析规则得到的模块绝对路径。

```javascript
define(function(require, exports, module) {

  console.log(module.uri); 
  // ==> http://example.com/path/to/this/file.js

});
```
一般情况下（没有在 `define` 中手写 id 参数时），`module.id` 的值就是 `module.uri`，两者完全相同。

module.dependencies `Array`

`dependencies` 是一个数组，表示当前模块的依赖。

module.exports `Object`

当前模块对外提供的接口。

传给 `factory` 构造方法的 `exports` 参数是 `module.exports` 对象的一个引用。只通过 `exports` 参数来提供接口，有时无法满足开发者的所有需求。 比如当模块的接口是某个类的实例时，需要通过 `module.exports` 来实现：

```javascript
define(function(require, exports, module) {

  // exports 是 module.exports 的一个引用
  console.log(module.exports === exports); // true

  // 重新给 module.exports 赋值
  module.exports = new SomeClass();

  // exports 不再等于 module.exports
  console.log(module.exports === exports); // false

});
```
注意：对 `module.exports` 的赋值需要同步执行，不能放在回调函数里。下面这样是不行的：

```javascript
// x.js
define(function(require, exports, module) {

  // 错误用法
  setTimeout(function() {
    module.exports = { a: "hello" };
  }, 0);

});
```
在 y.js 里有调用到上面的 x.js:

```javascript
// y.js
define(function(require, exports, module) {

  var x = require('./x');

  // 无法立刻得到模块 x 的属性 a
  console.log(x.a); // undefined

});
```
##AMD 模块定义规范
异步模块定义规范（AMD）制定了定义模块的规则，这样模块和模块的依赖可以被异步加载。这和浏览器的异步加载模块的环境刚好适应（浏览器同步加载模块会导致性能、可用性、调试和跨域访问等问题）。


####define() 函数

本规范只定义了一个函数 "define"，它是全局变量。函数的描述为：

```javascript
    define(id?, dependencies?, factory);
```

#####id
#####名字

第一个参数，id，是个字符串。它指的是定义中模块的名字，这个参数是可选的。如果没有提供该参数，模块的名字应该默认为模块加载器请求的指定脚本的名字。如果提供了该参数，模块名必须是“顶级”的和绝对的（不允许相对名字）。

#####模块名的格式

模块名用来唯一标识定义中模块，它们同样在依赖数组中使用。AMD的模块名规范是CommonJS模块名规范的超集。引用如下：

*模块名是由一个或多个单词以正斜杠为分隔符拼接成的字符串
*单词须为驼峰形式，或者"."，".."
*模块名不允许文件扩展名的形式，如".js"
*模块名可以为 "相对的" 或 "顶级的"。如果首字符为"."或".."则为"相对的"模块名
*顶级的模块名从根命名空间的概念模块解析
*相对的模块名从 "require" 书写和调用的模块解析
上文引用的CommonJS模块id属性常被用于JavaScript模块。

相对模块名解析示例：

*如果模块 `"a/b/c"` 请求 `"../d"`, 则解析为"a/d"
*如果模块 `"a/b/c"` 请求 `"./e"`, 则解析为`"a/b/e"`
如果AMD的实现支持加载器插件(Loader-Plugins),则"!"符号用于分隔加载器插件模块名和插件资源名。由于插件资源名可以非常自由地命名，大多数字符都允许在插件资源名使用。

（译注：关于Loader-Plugins）

#####依赖

第二个参数，dependencies，是个定义中模块所依赖模块的数组。依赖模块必须根据模块的工厂方法优先级执行，并且执行的结果应该按照依赖数组中的位置顺序以参数的形式传入（定义中模块的）工厂方法中。

依赖的模块名如果是相对的，应该解析为相对定义中的模块。换句话来说，相对名解析为相对于模块的名字，并非相对于寻找该模块的名字的路径。

本规范定义了三种特殊的依赖关键字。如果"require","exports", 或 "module"出现在依赖列表中，参数应该按照CommonJS模块规范自由变量去解析。

依赖参数是可选的，如果忽略此参数，它应该默认为["require", "exports", "module"]。然而，如果工厂方法的形参个数小于3，加载器会选择以函数指定的参数个数调用工厂方法。

#####工厂方法

第三个参数，factory，为模块初始化要执行的函数或对象。如果为函数，它应该只被执行一次。如果是对象，此对象应该为模块的输出值。

如果工厂方法返回一个值（对象，函数，或任意强制类型转换为true的值），应该为设置为模块的输出值。

#####简单的 CommonJS 转换

如果依赖性参数被忽略，模块加载器可以选择扫描工厂方法中的require语句以获得依赖性（字面量形为require("module-id")）。第一个参数必须字面量为require从而使此机制正常工作。

在某些情况下，因为脚本大小的限制或函数不支持toString方法（Opera Mobile是已知的不支持函数的toString方法），模块加载器可以选择扫描不扫描依赖性。

如果有依赖参数，模块加载器不应该在工厂方法中扫描依赖性。

####define.amd 属性

为了清晰的标识全局函数（为浏览器加载script必须的）遵从AMD编程接口，任何全局函数应该有一个"amd"的属性，它的值为一个对象。这样可以防止与现有的定义了define函数但不遵从AMD编程接口的代码相冲突。

当前，define.amd对象的属性没有包含在本规范中。实现本规范的作者，可以用它通知超出本规范编程接口基本实现的额外能力。

define.amd的存在表明函数遵循本规范。如果有另外一个版本的编程接口，那么应该定义另外一个属性，如define.amd2，表明实现只遵循该版本的编程接口。

一个如何定义同一个环境中允许多次加载同一个版本的模块的实现：

```javascript
    define.amd = {
      multiversion: true
    };
````
最简短的定义：
```javascript
    define.amd = {};
```
####一次输出多个模块

在一个脚本中可以使用多次define调用。这些define调用的顺序不应该是重要的。早一些的模块定义中所指定的依赖，可以在同一脚本中晚一些定义。模块加载器负责延迟加载未解决的依赖，直到全部脚本加载完毕，防止没必要的请求。

###例子

####使用 require 和 exports

创建一个名为"alpha"的模块，使用了require，exports，和名为"beta"的模块:

```javascript
   define("alpha", ["require", "exports", "beta"], function (require, exports, beta) {
       exports.verb = function() {
           return beta.verb();
           //Or:
           return require("beta").verb();
       }
   });
```
一个返回对象的匿名模块：

```javascript
   define(["alpha"], function (alpha) {
       return {
         verb: function(){
           return alpha.verb() + 2;
         }
       };
   });
```
一个没有依赖性的模块可以直接定义对象：

```javascript
   define({
     add: function(x, y){
       return x + y;
     }
   });
```
一个使用了简单CommonJS转换的模块定义：

```javascript
   define(function (require, exports, module) {
     var a = require('a'),
         b = require('b');

     exports.action = function () {};
   });
```
###全局变量

本规范保留全局变量"define"以用来实现本规范。包额外信息异步定义编程接口是为将来的CommonJS API保留的。模块加载器不应在此函数添加额外的方法或属性。

本规范保留全局变量"require"被模块加载器使用。模块加载器可以在合适的情况下自由地使用该全局变量。它可以使用这个变量或添加任何属性以完成模块加载器的特定功能。它同样也可以选择完全不使用"require"。

###使用注意

为了使静态分析工具（如build工具）可以正常工作，推荐使用字面上形如的'define(...)'。

###与CommonJS的关系

一个关于本API的wiki开始在CommonJS wiki中创建了，作为中转的格式，模块中转。但是为了包含模块定义接口，随着时间而不断改变。在CommonJS列表中关于推荐本API作为模块定义API尚未达成一致。本API被转移到它自己的wiki和讨论组中。

AMD可以作为CommonJS模块一个中转的版本只要CommonJS没有被用来同步的require调用。使用同步require调用的CommonJS代码可以被转换为使用回调风格的AMD模块加载器。

##ES6新特性：使用export和import实现模块化
在ES6前， 前端就使用RequireJS或者seaJS实现模块化， requireJS是基于AMD规范的模块化库，  而像seaJS是基于CMD规范的模块化库，  两者都是为了为了推广前端模块化的工具， 更多有关AMD和CMD的区别， 后面参考给了几个链接；

现在ES6自带了模块化， 也是JS第一次支持module， 在很久以后 ，我们可以直接作用import和export在浏览器中导入和导出各个模块了， 一个js文件代表一个js模块；

现代浏览器对模块(module)支持程度不同， 目前都是使用babelJS， 或者Traceur把ES6代码转化为兼容ES5版本的js代码;

###ES6的模块化的基本规则或特点：
　ES6的模块化的基本规则或特点， 欢迎补充：

　　　　1.每一个模块只加载一次， 每一个JS只执行一次， 如果下次再去加载同目录下同文件，直接从内存中读取。 一个模块就是一个单例，或者说就是一个对象；

　　　　2.每一个模块内声明的变量都是局部变量， 不会污染全局作用域；

　　　　3.模块内部的变量或者函数可以通过export导出；

　　　　4.一个模块可以导入别的模块

```javascript
//lib.js
//导出常量
export const sqrt = Math.sqrt;
//导出函数
export function square(x) {
    return x * x;
}
//导出函数
export function diag(x, y) {
    return sqrt(square(x) + square(y));
}

//main.js
import { square, diag } from './lib';
console.log(square(11)); // 121
console.log(diag(4, 3)); // 5
```
###import和export的基本语法：
第一种导出的方式：

　　在lib.js文件中， 使用 `export{接口}` 导出接口， 大括号中的接口名字为上面定义的变量， `import`和`export`是对应的；

```javascript
//lib.js 文件
let bar = "stringBar";
let foo = "stringFoo";
let fn0 = function() {
    console.log("fn0");
};
let fn1 = function() {
    console.log("fn1");
};
export{ bar , foo, fn0, fn1}

//main.js文件
import {bar,foo, fn0, fn1} from "./lib";
console.log(bar+"_"+foo);
fn0();
fn1();
```
　第二种导出的方式：

 　　在export接口的时候， 我们可以使用 XX as YY， 把导出的接口名字改了， 比如： `closureFn as sayingFn`， 把这些接口名字改成不看文档就知道干什么的：

```javascript
//lib.js文件
let fn0 = function() {
    console.log("fn0");
};
let obj0 = {}
export { fn0 as foo, obj0 as bar};

//main.js文件
import {foo, bar} from "./lib";
foo();
console.log(bar);
```

第三种导出的方式：

　　这种方式是直接在export的地方定义导出的函数，或者变量：

```javascript
//lib.js文件
export let foo = ()=> {console.log("fnFoo") ;return "foo"},bar = "stringBar";

//main.js文件
import {foo, bar} from "./lib";
console.log(foo());
console.log(bar);
```
第四种导出的方式：

　　这种导出的方式不需要知道变量的名字， 相当于是匿名的， 直接把开发的接口给export；
　　如果一个js模块文件就只有一个功能， 那么就可以使用export default导出；
```javascript
//lib.js
export default "string";

//main.js
import defaultString from "./lib";
console.log(defaultString);
```
第五种导出方式：

　　export也能默认导出函数， 在import的时候， 名字随便写， 因为每一个模块的默认接口就一个：

```javascript
//lib.js
let fn = () => "string";
export {fn as default};

//main.js
import defaultFn from "./lib";
console.log(defaultFn());
```
第六种导出方式：

　　使用通配符*  ,重新导出其他模块的接口 (其实就是转载文章， 然后不注明出处啦);

```javascript
//lib.js
export * from "./other";
//如果只想导出部分接口， 只要把接口名字列出来
//export {foo,fnFoo} from "./other";

//other.js
export let foo = "stringFoo", fnFoo = function() {console.log("fnFoo")};

//main.js
import {foo, fnFoo} from "./lib";
console.log(foo);
console.log(fnFoo());
```
其他：ES6的import和export提供相当多导入以及导出的语法；

　　在import的时候可以使用通配符*导入外部的模块：

```javascript
import * as obj from "./lib";
console.log(obj);
```
###ES6导入的模块都是属于引用：
每一个导入的js模块都是活的， 每一次访问该模块的变量或者函数都是最新的， 这个是原生ES6模块 与AMD和CMD的区别之一,以下代码修改自http://exploringjs.com/es6/ch_modules.html#_imports-are-read-only-views-on-exports

```javascript
//lib.js
export let counter = 3;
export function incCounter() {
    counter++;
}
export function setCounter(value) {
    counter = value;
}


//main.js
import { counter, incCounter ,setCounter} from './lib';

// The imported value `counter` is live
console.log(counter); // 3
incCounter();
console.log(counter); // 4
setCounter(0);
console.log(counter); // 0
```
在main.js中， counter一直指向lib.js中的局部变量counter， 按照JS的尿性， 像数字或者字符串类型或者布尔值的原始值要被复制， 而不是赋址；

###循环依赖的问题：
NodeJS的循环依赖是这么处理的：打开；

　　循环依赖是JS模块化带来的问题， 在浏览器端， 使用RequireJS测试模块化， 比如有一个文件file0.js依赖于file1.js， 而file1.js又依赖于file0.js， 那么file0.js和file1.js到底谁先执行？

```javascript
//index.html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8"/>
</head>
<body>

<script data-main="cyclic" src="//cdn.bootcss.com/require.js/2.2.0/require.min.js"></script>
<script>
//cyclic.js
require(["file0"], function(file0) {
    console.log(file0)
})

//file0.js
define(["file1"], function(file1) {
    console.log(file1)
    return {
        file0 : "file0"
    }
})

//file1.js
define(["file0"], function(file0) {
    console.log(file0);
    return {
        file1 : "file1"
    }
})
</script>
</body>
</html>
```
在控制台的依次输出为：

```javascript
undefined
Object { file1: "file1" } 
Object { file0: "file0" }
```
在执行file1.js的时候file0.js还没执行完， 所以输出了undefined， 这种输出结果和NodeJS输出的情况是一样的；

然后我又使用了司徒大神的mass-framework框架试了一下， 司徒大神的框架直接提示我： "模块与之前的某些模块存在循环依赖"， 这样还比较好点， requireJS对于循环依赖是直接执行循环依赖的模块， 会导致在开发的时候给自己挖坑....；

接下来我又在babel-node下进行测试：下面是几个测试，可以无视：

我使用ES6的模块试一试， 只要每一个模块被引用， 无论模块是否执行完毕， 该模块的export已经被导出了， 如果导出的是函数：

```javascript
//cyclic.js
import fn0 from "./file0";
fn0();

//file0.js
import fn1 from "./file1";
fn1();
console.log("file0.js runs");
export default function() {console.log("file0 export runs")}

//file1.js
import fn0 from "./file0";
fn0();
console.log("file1.js runs");
export default function() {console.log("file1 export runs")}
```
如果导出的是字符串：

```javascript
//cyclic.js
import str from "./file0";
console.log(str);

//file0.js
import str1 from "./file1";
console.log(str1)
console.log("file0.js runs");
export default "str0";

//file1.js
import str0 from "./file0";
console.log(str0)
console.log("file1.js runs");
export default "str1";
```

如果导出的是对象：

那么第一行会先输出一个初始值{},在最后等待file0.js和file1.js执行完毕以后， 才输出file0.js导出的对象；

如果是数组：

那么第一行会输出一个被静态分析过的初始值undefined,在最后等待file0.js和file1.js执行完毕以后， 才输出file0.js导出的对象；

如果是布尔值：

那么第一行会输出一个被静态分析过的初始值undefined,在最后等待file0.js和file1.js执行完毕以后， 才输出file0.js导出的布尔值；

 

为什么会这样呢？ 我好像在这边找到了答案：http://exploringjs.com/es6/ch_modules.html#_modules ，ES6的import和export被提前到js的最顶层， 在函数或者对象，或者基本值被导出去的时候提前被静态分析过，参考：http://www.ecma-international.org/ecma-262/6.0/#sec-parsemodule , http://www.ecma-international.org/ecma-262/6.0/#sec-toplevelmoduleevaluationjob

结论：用ES6的export导出数据接口的时候， 最好统一用函数， 避免在循环依赖的时候， 因为JS会把不同类型的对象静态解析成不同的初始值;

####浏览器兼容：
 
    chrome浏览器目前不支持import，和export；
    
    火狐的支持也有限， 比chrome好；
    
    