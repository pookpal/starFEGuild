# 通用约定

#### 注释
**原则**
- As short as possible（如无必要，勿增注释）：尽量提高代码本身的清晰性、可读性。
- As long as necessary（如有必要，尽量详尽）：合理的注释、空行排版等，可以让代码更易阅读、更具美感。

**单行注释**

必须独占一行。`//` 后跟一个空格，缩进与下一行被注释说明的代码一致。

**多行注释**

避免使用 `/*...*/` 这样的多行注释。有多行注释内容时，使用多个单行注释。

**函数/方法注释**
1. 函数/方法注释必须包含函数说明，有参数和返回值时必须使用注释标识。；
2. 参数和返回值注释必须包含类型信息和说明；
3. 当函数是内部函数，外部不可访问时，可以使用 @inner 标识；

```javascript
/**
 * 函数描述
 *
 * @param {string} p1 参数1的说明
 * @param {string} p2 参数2的说明，比较长
 *     那就换行了.
 * @param {number=} p3 参数3的说明（可选）
 * @return {Object} 返回值描述
 */
function foo(p1, p2, p3) {
    var p3 = p3 || 10;
    return {
        p1: p1,
        p2: p2,
        p3: p3
    };
}
```

**文件注释**

文件注释用于告诉不熟悉这段代码的读者这个文件中包含哪些东西。 应该提供文件的大体内容, 它的作者, 依赖关系和兼容性信息。如下:

```javascript
/**
 * @fileoverview Description of file, its uses and information
 * about its dependencies.
 * @author user@meizu.com (Firstname Lastname)
 * Copyright 2009 Meizu Inc. All Rights Reserved.
 */
```

#### 命名
**驼峰式命名法介绍：**
    
   驼峰式命名法由小(大)写字母开始，后续每个单词首字母都大写。
   按照第一个字母是否大写，分为：
   ① Pascal Case 大驼峰式命名法：首字母大写。eg：StudentInfo、UserInfo、ProductInfo
   ② Camel Case 小驼峰式命名法：首字母小写。eg：studentInfo、userInfo、productInfo

**变量**, 使用 Camel 命名法。

```javascript
var loadingModules = {};
```

**私有属性、变量和方法**以下划线 _ 开头。
```javascript
var _privateMethod = {};
```

**常量**, 使用全部字母大写，单词间下划线分隔的命名方式。

```javascript
var HTML_ENTITY = {};
```

1. **函数**, 使用 Camel 命名法。
2. 函数的**参数**, 使用 Camel 命名法。

```javascript
function stringFormat(source) {}

function hear(theBells) {}
```

1. **类**, 使用 Pascal 命名法
2. 类的 **方法 / 属性**, 使用 Camel 命名法

```javascript
function TextNode(value, engine) {
    this.value = value;
    this.engine = engine;
}

TextNode.prototype.clone = function () {
    return this;
};
```

1. **枚举变量** 使用 Pascal 命名法。
2. **枚举的属性**， 使用全部字母大写，单词间下划线分隔的命名方式。

```javascript
var TargetState = {
    READING: 1,
    READED: 2,
    APPLIED: 3,
    READY: 4
};
```

由多个单词组成的 **缩写词**，在命名中，根据当前命名法和出现的位置，所有字母的大小写与首字母的大小写保持一致。

```javascript
function XMLParser() {}

function insertHTML(element, html) {}

var httpRequest = new HTTPRequest();
```

**文件名**
文件名应该使用小写字符, 以避免在有些系统平台上不识别大小写的命名方式. 文件名以.js结尾, 不要包含除 - 和 _ 外的标点符号(使用 - 优于 _).

#### 命名语法
**类名**，使用名词。

```javascript
function Engine(options) {}
```

**函数名**，使用动宾短语。

```javascript
function getStyle(element) {}
```

**boolean** 类型的变量使用 is 或 has 开头。

```javascript
var isReady = false;
var hasMoreCommands = false;
```

**Promise 对象**用动宾短语的进行时表达。

```javascript
var loadingData = ajax.get('url');
loadingData.then(callback);
```

#### 接口命名规范

1. 可读性强，见名晓义；
2. 尽量不与 jQuery 社区已有的习惯冲突；
3. 尽量写全。不用缩写，除非是下面列表中约定的；（变量以表达清楚为目标，uglify 会完成压缩体积工作）

| 常用词 | 说明 |
| -- | -- |
| options | 表示选项，与 jQuery 社区保持一致，不要用 config, opts 等 |
| active | 表示当前，不要用 current 等 |
| index | 表示索引，不要用 idx 等 |
| trigger | 触点元素 |
| triggerType | 触发类型、方式 |
| context | 表示传入的 this 对象 |
| object | 推荐写全，不推荐简写为 o, obj 等 |
| element | 推荐写全，不推荐简写为 el, elem 等 |
| length | 不要写成 len, l |
| prev | previous 的缩写 |
| next | next 下一个 |
| constructor |	不能写成 ctor |
| easing | 示动画平滑函数 |
| min |	minimize 的缩写 |
| max |	maximize 的缩写 |
| DOM |	不要写成 dom, Dom |
| .hbs | 使用 hbs 后缀表示模版 |
| btn |	button 的缩写 |
| link| 超链接 |
| title	| 主要文本 |
| img | 图片路径（img标签src属性）|
| dataset |	html5 data-xxx 数据接口 |
| theme | 主题 |
| className	| 类名 |
| classNameSpace | class 命名空间 |

#### True 和 False 布尔表达式
类型检测优先使用 typeof。对象类型检测使用 instanceof。null 或 undefined 的检测使用 == null。

下面的布尔表达式都返回 false:

* null
* undefined
* '' 空字符串
* 0 数字0

但小心下面的, 可都返回 true:

* '0' 字符串0
* [] 空数组
* {} 空对象

#### 不要在 Array 上使用 for-in 循环
for-in 循环只用于 `object/map/hash` 的遍历, 对 `Array` 用 for-in 循环有时会出错. 因为它并不是从 0 到 length - 1 进行遍历, 而是所有出现在对象及其原型链的键值。
```javascript
// Not recommended
function printArray(arr) {
  for (var key in arr) {
    print(arr[key]);
  }
}

printArray([0,1,2,3]);  // This works.

var a = new Array(10);
printArray(a);  // This is wrong.

a = document.getElementsByTagName('*');
printArray(a);  // This is wrong.

a = [0,1,2,3];
a.buhu = 'wine';
printArray(a);  // This is wrong again.

a = new Array;
a[3] = 3;
printArray(a);  // This is wrong again.

// Recommended
function printArray(arr) {
  var l = arr.length;
  for (var i = 0; i < l; i++) {
    print(arr[i]);
  }
}
```

#### 二元和三元操作符
操作符始终写在前一行, 以免**分号的隐式插入**产生预想不到的问题。

```javascript
var x = a ? b : c;

var y = a ?
    longButSimpleOperandB : longButSimpleOperandC;

var z = a ?
        moreComplicatedB :
        moreComplicatedC;
```

`.` 操作符也是如此：

```javascript
var x = foo.bar().
    doSomething().
    doSomethingElse();
```

#### 条件(三元)操作符 (?:)
三元操作符用于替代 if 条件判断语句。

```javascript
// Not recommended
if (val != 0) {
  return foo();
} else {
  return bar();
}

// Recommended
return val ? foo() : bar();
```

#### && 和 ||
二元布尔操作符是可短路的, 只有在必要时才会计算到最后一项。

```javascript
// Not recommended
function foo(opt_win) {
  var win;
  if (opt_win) {
    win = opt_win;
  } else {
    win = window;
  }
  // ...
}

if (node) {
  if (node.kids) {
    if (node.kids[index]) {
      foo(node.kids[index]);
    }
  }
}

// Recommended
function foo(opt_win) {
  var win = opt_win || window;
  // ...
}

var kid = node && node.kids && node.kids[index];
if (kid) {
  foo(kid);
}
```

####前端资源Build
项目必须总是提供一些通用的方法来检验（can be linted）、测试和压缩源码以为产品阶段使用做准备。对于此类工作可以使用Grunt或者Gulp配合相应的插件来实现。通过简单的配置即可完成自动对CSS进行检查/压缩/合并，对JS进行检查/压缩/合并，对html文件进行压缩，删除创建目录，拷贝文件，压缩打包等，十分方便。

####项目规范
1.	JS/CSS 文件编码统一采用 UTF8编码
2.	JS/CSS 最终发布到产品中的时候需要被压缩，以减小静态资源文件大小，提升页面加载速度
3.	JS里声明变量必须加上 var 关键字，推荐一个 var 同时声明多个变量，或者一组有逻辑关系的变量，避免一个变量一个var.
4.	没有特殊原因避免使用 with/eval
5.	字符串拼接在少量(次数为个位数)的情况下可以使用'+', 大量的时候使用数组 join(), 或者尽可能采用模板引擎渲染：比如jsRender等, 如果是Extjs可以采用XTemplate.
6.	for循环遍历：for(var i = 0, l = arr.length; i < l; i++){// doSomething here } 采用这种方式而不是 i < arr.length, 前一种方式只会计算一次 arr 的长度，而后一种方式会计算数组长度 arr.length + 1 次，效率比较低
7.	变量比较的时候总是判断最好、最精确的值，推荐使用===少用==(可以参考jQuery代码里面, 可以看到只有在== null的时候才可能使用==，其他情况一律使用的是===).
8.	JS里变量命名规范使用 functionNamesLikeThis, variableNamesLikeThis, ClassNamesLikeThis, EnumNamesLikeThis,methodNamesLikeThis, 和 SYMBOLIC_CONSTANTS_LIKE_THIS, 尤其不要跟python里面的变量命名方式混淆了.
9.	JS模块里面私有的方法前面可以加_,公有方法不加，但是函数内的局部变量就没必要加_了；
10.	JS文件名应该使用小写字符, 以避免在有些系统平台上不识别大小写的命名方式. 文件名以.js结尾, 不要包含除 - 和 _ 外的标点符号(使用 - 优于 _), 我们约定统一使用js-file-name.js这种类型，对于template文件命名方式为template_name.html形式.
11.	HTML DOM节点上原则上不允许有内嵌的style，样式要尽可能抽出来共用。有些样式定义可以成为全局的，比如字体颜色、大小之类的；有些样式不能公用则应当给相应的页面一个class命名空间，任何针对这个页面的样式都应该在这个命名空间下面定义，这样不至于影响到其他页面。
12.	公共的js第三方类库放在static/js/common/lib下，jQuery相关类库放在static/js/common/lib/jQplugin下，我们自己开发的公共类库放在static/js/common下
13.	不要使用魔法数字，尽量定义一个常量来表示该数字，并加上相应的注释，否则后期可能出现因为数字变化而导致牵一发而动全身，需要到处修改，增加维护成本
14.	在开发相应功能的时候尽量抽象化、组件化、通用化：考虑这个东西其他地方会不会用到，能不能做成一个组件？而不是类似的代码到处复制、修改或者让大家都去写一遍
15.	类似地，在解决问题的时候要考虑下其他地方会不会存在同样的问题？能不能统一解决掉？尤其对于类似ExtJs的Bug这种，能不能做最少的改动解决所有同样的问题,类似于全局补丁.
16.	代码风格跟其他JS文件的代码风格保持一致;
