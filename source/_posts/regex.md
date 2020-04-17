---
title: "正则表达式"
entitle: regex
author: haohaio
avatar: /images/favicon.png
authorLink: "https://haohaio.github.io"
authorAbout: "https://haohaio.github.io"
authorDesc: 写写代码，记记笔记
categories: 前端
timestamp: 1587092288
comments: true
date: 2020-04-18 01:13:08
tags: JavaScript
keywords:
description:
photos:
  - /img/tags/javascript.jpg
---

我们在开发过程中经常会用到正则表达式，比如输入校验，比如字符串替换等。可能许多人包括我自己，需要正则表达式的时候都是到到网上去找一个满足我们要求的正则就行了，但真让我们自己写正则的话还是有些困难的。今天就来系统的学习一下正则表达式，今后当需要我们手写正则的时候，自己也能码出来~~

> 正则表达式是一组由字母和符号组成的特殊文本，它可以用来从文本中找出满足你想要的格式的句子。

### 基本匹配

例如：一个正则表达式 the，它表示一个规则：由字母 t 开始，接着是 h，再接着是 e。

在 JavaScript 中，我们可以使用字符串的 `match` 方法来查找要匹配字符串：

```js
var str = "The fat cat sat on the mat.";
var regex = /the/;
var res = str.match(regex);
console.log(res); // ["the", index: 19, input: "The fat cat sat on the mat.", groups: undefined]
```

可以看到正则表达式是大小写敏感的，不会匹配到字符串开头的 `The`，而是匹配到后面的 `the`。

### 元字符

正则表达式主要依赖于元字符。 元字符不代表他们本身的字面意思，他们都有特殊的含义。一些元字符写在方括号中的时候有一些特殊的意思。以下是一些元字符的介绍：

| 元字符 | 描述                                                                          |
| :----: | ----------------------------------------------------------------------------- |
|   .    | 匹配任意单个字符除了换行符。                                                  |
|  [ ]   | 字符种类。匹配方括号内的任意字符。                                            |
|  [^ ]  | 否定的字符种类。匹配除了方括号里的任意字符                                    |
|   \*   | 匹配>=0 个重复的在\*号之前的字符。                                            |
|   +    | 匹配>=1 个重复的+号前的字符。                                                 |
|   ?    | 标记?之前的字符为可选。                                                       |
| {n,m}  | 匹配 num 个大括号之前的字符或字符集 (n <= num <= m)。                         |
| (xyz)  | 字符集，匹配与 xyz 完全相等的字符串。                                         |
| &#124; | 或运算符，匹配符号前或后的字符。                                              |
| &#92;  | 转义字符,用于匹配一些保留的字符 <code>[ ]() { } . \* + ? ^ \$ \ &#124;</code> |
|   ^    | 从开始行开始匹配。                                                            |
|   \$   | 从末端开始匹配。                                                              |

#### 点运算符 `.`

`.` 匹配任意单个字符，但不匹配换行符。

例如，表达式 `.at` 匹配一个任意字符后面跟着是 `a` 和 `t` 的字符串。

```js
var str = "The car parked in the garage.";
var regex = /.ar/;
var res = str.match(regex);
console.log(res); // ["car", index: 4, input: "The car parked in the garage.", groups: undefined]
```

> 表达式不设置全部搜索, 即使用修饰符 `g` 时，匹配到第一个字符串后就会停止匹配。

#### 字符集 `[]`

字符集也叫做字符类。 方括号用来指定一个字符集。在方括号中的字符集不关心顺序。

例如，表达式`[Tt]he` 匹配 `the` 和 `The`：

```js
var str = "The car parked in the garage.";
var regex = /[Tt]he/g;
var res = str.match(regex);
console.log(res); // ["The", "the"]
```

在方括号中使用连字符来指定字符集的范围。

例如：`[A-Za-z]he` 匹配任意英文字母后接着是 h，再接着是 e。

```js
var str = "The car parked in the garage.";
var regex = /[A-Za-z]he/g;
var res = str.match(regex);
console.log(res); // ["The", "the"]
```

方括号中的 `.` 就表示 `.` 字符，`ar[.]` 就是单纯的匹配 `ar.` 字符串。

```js
var str = "A garage is a good place to park a car.";
var regex = /ar[.]/g;
var res = str.match(regex);
console.log(res); // ["ar."]
```

#### 否定字符集 `[^ ]`

`^` 用在方括号的开头时，它表示这个字符集是否定的。 例如，表达式 `[^c]ar` 匹配一个后面跟着 ar 的除了 c 的任意字符。

```js
var str = "The car parked in the garage.";
var regex = /[^c]ar/g;
var res = str.match(regex);
console.log(res); // ["par", "gar"]
```

举一个常见的栗子，使用字符串的 `replace` 方法，去除字符串中的非英文字符：

```js
var str = "The car parked in the garage.@123";
var regex = /[^A-Za-z]/g;
str = str.replace(regex, "");
console.log(str); // Thecarparkedinthegarage
```

#### 重复次数

##### `*` 号

`*` 号匹配 在 `*` 之前的字符出现 `大于等于0` 次。例如，表达式 `a*` 匹配 0 或更多个以 a 开头的字符。表达式`[a-z]*` 匹配一个行中所有以小写字母开头的字符串。

```js
var str = "The car parked in the garage.";
var regex = /[a-z]*/g;
var res = str.match(regex);
console.log(res); // ["", "he", "", "car", "", "parked", "", "in", "", "the", "", "garage", "", ""]
```

`*` 字符和 `.` 字符搭配可以匹配所有的字符 `.*`。

`*` 和表示匹配空格的符号 `\s` 连起来用，如表达式 `\s*cat\s*` 匹配 0 或更多个空格开头和 0 或更多个空格结尾的 cat 字符串。

```js
var str = "The fat cat sat on the concatenation.";
var regex = /\s*cat\s*/g;
var res = str.match(regex);
console.log(res); // [" cat ", "cat"]
```

##### `+` 号

`+`号匹配`+`号之前的字符出现 >=1 次。

例如表达式`c.+t` 匹配以首字母`c`开头以`t`结尾，中间跟着至少一个字符的字符串。

```js
var str = "The fat cat sat on the mat.";
var regex = /c.+t/g;
var res = str.match(regex);
console.log(res); // ["cat sat on the mat"]
```

##### `?` 号

在正则表达式中元字符 `?` 标记在符号前面的字符为可选，即出现 0 或 1 次。

例如，表达式 `[T]?he` 匹配字符串 `he` 和 `The`：

```js
var str = "The fat cat sat on the mat.";
var regex = /[T]?he/g;
var res = str.match(regex);
console.log(res); // ["The", "he"]
```

可以看到 `*` 号是匹配 0 个或多个，`?` 号是匹配 0 个或 1 个。

#### `{}` 号

在正则表达式中 `{}` 是一个量词，常用来限定一个或一组字符可以重复出现的次数。

例如， 表达式 `[0-9]{2,3}` 匹配最少 2 位最多 3 位 0~9 的数字：

```js
var str = "The number was 9.9997 but we rounded it off to 10.0.";
var regex = /[0-9]{2,3}/g;
var res = str.match(regex);
console.log(res); // ["999", "10"]
```

我们可以省略第二个参数。 例如，`[0-9]{2,}` 匹配至少两位 0~9 的数字。

```js
var str = "The number was 9.9997 but we rounded it off to 10.0.";
var regex = /[0-9]{2,}/g;
var res = str.match(regex);
console.log(res); // ["9997", "10"]
```

如果逗号也省略掉则表示重复固定的次数。例如，`[0-9]{2}` 匹配 2 位数字：

```js
var str = "The number was 9.9997 but we rounded it off to 10.0.";
var regex = /[0-9]{2}/g;
var res = str.match(regex);
console.log(res); // ["99", "97", "10"]
```

#### `(...)` 特征标群

`(...)` 小括号中包含的内容将会被看成一个整体。

例如, 表达式 `(ab)*` 匹配连续出现 0 或更多个 `ab`。如果没有使用 `(...)` ，那么表达式 `ab*` 将匹配 `a` 后面连续出现 0 或更多个 `b`：

```js
var str = "ab abbb abab";
var regex = /(ab)*/g;
var res = str.match(regex);
console.log(res); // ["ab", "", "ab", "", "", "", "abab", ""]

var str = "ab abbb abab";
var regex = /ab*/g;
var res = str.match(regex);
console.log(res); // ["ab", "abbb", "ab", "ab"]
```

我们还可以在 `()` 中用或字符 `|` 表示或。例如，`(c|g|p)ar` 匹配 `car` 或 `gar` 或 `par`：

```js
var str = "The car is parked in the garage.";
var regex = /(c|g|p)ar/g;
var res = str.match(regex);
console.log(res); // ["car", "par", "gar"]
```

#### `|` 或运算符

或运算符就表示或，用作判断条件。

例如 `(T|t)he|car` 匹配 `(T|t)he` 或 `car`：

```js
var str = "The car is parked in the garage.";
var regex = /(T|t)he|car/g;
var res = str.match(regex);
console.log(res); // ["The", "car", "the"]
```

#### 转码特殊字符

反斜线 `\` 在表达式中用于转码紧跟其后的字符。用于指定 `{ } [ ] / \ + * . $ ^ | ?` 这些特殊字符。如果想要匹配这些特殊字符则要在其前面加上反斜线 `\`。

例如 `.` 是用来匹配除换行符外的所有字符的。如果想要匹配句子中的 `.` 则要写成 `\.`：

```js
var str = "The fat cat sat on the mat.";
var regex = /(f|c|m)at\.?/g;
var res = str.match(regex);
console.log(res); // ["fat", "cat", "mat."]
```

#### 锚点

在正则表达式中，想要匹配指定开头或结尾的字符串就要使用到锚点。`^` 指定开头，`$` 指定结尾。

```js
var str = "The car is parked in the garage.";
var regex = /^(T|t)he/g;
var res = str.match(regex);
console.log(res); // ["The"]

var str = "The car is parked in the garage.";
var regex = /^the/g;
var res = str.match(regex);
console.log(res); // null
```

```js
var str = "The fat cat. sat. on the mat.";
var regex = /(at\.)$/g;
var res = str.match(regex);
console.log(res); // ["at."]
```

### 简写字符集

正则表达式提供一些常用的字符集简写。如下:

| 简写 | 描述                                                |
| :--: | --------------------------------------------------- |
|  .   | 除换行符外的所有字符                                |
|  \w  | 匹配所有字母数字和下划线 `_`，等同于 `[a-zA-Z0-9_]` |
|  \W  | 匹配所有非字母数字，即符号，等同于： `[^\w]`        |
|  \d  | 匹配数字： `[0-9]`                                  |
|  \D  | 匹配非数字： `[^\d]`                                |
|  \s  | 匹配所有空格字符，等同于： `[\t\n\f\r\p{Z}]`        |
|  \S  | 匹配所有非空格字符： `[^\s]`                        |
|  \f  | 匹配一个换页符                                      |
|  \n  | 匹配一个换行符                                      |
|  \r  | 匹配一个回车符                                      |
|  \t  | 匹配一个制表符                                      |
|  \v  | 匹配一个垂直制表符                                  |
|  \p  | 匹配 CR/LF（等同于 `\r\n`），用来匹配 DOS 行终止符  |

> \w 会匹配到 `_`，一般我们匹配所有字母数字时，会使用`[a-zA-Z0-9]`

```js
var str = "123_";
var regex = /\w/g;
var res = str.match(regex);
console.log(res); // ["at."]
```

### 零宽度断言（前后预查）

#### `?=...` 正先行断言

`?=...` 正先行断言，表示第一部分表达式之后必须跟着 `?=...`定义的表达式。返回结果只包含满足匹配条件的第一部分表达式。定义一个正先行断言要使用 `()`。在括号内部使用一个问号和等号： `(?=...)`。

举个栗子理解一下，`(T|t)he(?=\sfat)` 表示匹配 `The` 和 `the` 后面紧跟着 `(空格)fat`：

```js
var str = "The fat cat sat on the mat.";
var regex = /(T|t)he(?=\sfat)/g;
var res = str.match(regex);
console.log(res); // ["The"]
```

#### `?!...` 负先行断言

负先行断言 `?!` 用于筛选所有匹配结果，筛选条件为 其后不跟随着断言中定义的格式。

举个栗子理解一下，`(T|t)he(?!\sfat)` 表示匹配 `The` 和 `the` 后面不跟着 `(空格)fat`：

```js
var str = "The fat cat sat on the mat.";
var regex = /(T|t)he(?!\sfat)/g;
var res = str.match(regex);
console.log(res); // ["the"]
```

#### `?<= ...` 正后发断言

正后发断言 记作`(?<=...)` 用于筛选所有匹配结果，筛选条件为其前跟随着断言中定义的格式。

举个栗子理解一下，表达式 `(?<=(T|t)he\s)(fat|mat)` 匹配 `fat` 和 `mat`，且其前跟着 `The` 或 `the`：

```js
var str = "The fat cat sat on the mat.";
var regex = /(?<=(T|t)he\s)(fat|mat)/g;
var res = str.match(regex);
console.log(res); // ["fat", "mat"]
```

#### `?<!...` 负后发断言

负后发断言 记作 `(?<!...)` 用于筛选所有匹配结果，筛选条件为 其前不跟随着断言中定义的格式。

```js
var str = "The cat sat on cat.";
var regex = /(?<!(T|t)he\s)(cat)/g;
var res = str.match(regex);
console.log(res); // ["cat"]
```

### 标志

标志也叫模式修正符，因为它可以用来修改表达式的搜索结果。 这些标志可以任意的组合使用，它也是整个正则表达式的一部分。

| 标志 | 描述                                                  |
| :--: | ----------------------------------------------------- |
|  i   | 忽略大小写。                                          |
|  g   | 全局搜索。                                            |
|  m   | 多行修饰符：锚点元字符 `^` `$` 工作范围在每行的起始。 |

#### 忽略大小写 `i`

`g` 我们前面已经一直在用了，看下和 `i` 组合一起使用吧：

```js
var str = "The fat cat sat on the mat.";
var regex = /The/gi;
var res = str.match(regex);
console.log(res); // ["The", "the"]
```

#### 多行修饰符 `m`

像之前介绍的 `(^,$)` 用于检查格式是否是在待检测字符串的开头或结尾。但我们如果想要它在每行的开头和结尾生效，我们需要用到多行修饰符 `m`。

```js
var str = `The fat
          cat sat
          on the mat.`;
var regex = /.at(.)?$/;
var res = str.match(regex);
console.log(res); // ["mat."]

var regex = /.at(.)?$/;
var res = str.match(regex);
console.log(res); // ["fat", "sat", "mat."]
```

### 贪婪匹配与惰性匹配

正则表达式默认采用贪婪匹配模式，在该模式下意味着会匹配尽可能长的子串。我们可以使用 ? 将贪婪匹配模式转化为惰性匹配模式。

```js
var str = "The fat cat sat on the mat.";
var regex = /(.*at)/g;
var res = str.match(regex);
console.log(res); // ["The fat cat sat on the mat"]

var regex = /(.*?at)/g;
var res = str.match(regex);
console.log(res); // ["The fat", " cat", " sat", " on the mat"]
```

### 练习

接下来我们来练习写一下我们经常会用到的正则吧。

#### 正整数

正整数的规则很简单，前面有 0 个或 1 个 `+` 号，因此我们可以用 `?` 号 或 `{0, 1}` 进行校验；此外匹配 `+` 号时，需要使用 `\` 进行转义。后面至少有一个 0-9 的数字，因此我们可以用 `[0-9]+` 或 `\d+` 来进行表示，接下来我们写一个校验是否是正整数的方法，方便进行测试：

```js
function isPositiveInteger(str) {
  let regex = /^\+?\d+$/;
  return regex.test(str);
}

isPositiveInteger("3"); // true
isPositiveInteger("+3"); // true
isPositiveInteger("+3a"); // false
```

是不是 so easy ~~

#### 手机号

现在的手机号都是以 1 开头，第二位可以是 3-9 的数字，后面是 9 位数字，接下来我们就自己码一下，写一个校验手机号的方法：

```js
function isMobile(str) {
  let regex = /^1[3-9]\d{9}$/;
  return regex.test(str);
}

isMobile("18123456789"); // true
isMobile("12123456789"); // false
isMobile("181234567890"); // false
```

没什么问题 ~~

#### 身份证号

接下来我们在来写一下身份证号的正则，身份证一共 18 位，首位是 1-9 的数字，2-6 位是 0-9 的数字，7-14 位是出生日期，15-17 位是 0-9 的数字，末位是 0-9 的数字或 x。需要注意的是我们还需要校验一下出生日期格式。

这里我们只对出生日期做一下简单校验，就不做月份对应日期的相关校验了。具体规则为：出生日期年份为 18|19|20 开头，后两位为 0-9 的数字，正则可以写成 `(18|19|20)\d{2}`。月份为 01-12，正则可以写成 `(0[1-9])|(1[0-2])`，日期为 0-31, 正则可以写成 `(([0-2][1-9])|10|20|30|31)`

```js
function isIdCard(str) {
  let regex = /^[1-9]\d{5}(18|19|20)\d{2}(0[1-9])|(1[0-2])(([0-2][1-9])|10|20|30|31)\d{3}[0-9Xx]$/;
  return regex.test(str);
}

isIdCard("41112319921008111x"); // true
isIdCard("41112319921032111x"); // false
isIdCard("411123199210011111x"); // false
```

验证一下，搞定 ~~
