# 理解 JavaScript 的 ES6 特性

> 原文：<https://medium.com/codex/understanding-the-es6-features-of-javascript-569bbd874fd1?source=collection_archive---------11----------------------->

![](img/9f5d7cf3c1cce28c6bd6877d4e5c1687.png)

图像来源- [去飞溅](https://unsplash.com/s/photos/coding)

ECMAScript 6 缩写为 ES6。ES6 是 ECMAScript 的第六个版本，于 2015 年发布，被称为 ECMAScript 2015。

现在，让我们看看 ES6 中包含了什么

# **箭头功能**

对于编写函数表达式，箭头函数提供了简化的语法。
函数关键字、返回关键字和花括号不是必需的。

考虑一个函数声明的例子:

```
//function declarationfunction favouriteFood() {return "I'm going to have pizza for lunch";}
```

使用箭头功能:

```
const lunchMenu = pizza=> `I'm going to eat a ${pizza} for lunch`;console.log( lunchMenu("onion") );
```

# **模板文字**

带有嵌入表达式的字符串文字称为模板文字。多行字符串和字符串插值功能可以与它们一起使用。在 ES2015 规范的先前版本中，它们被称为“**模板字符串**”

```
let num1 = 2;let num2 = 3;const fullName = `${num1 + num2}`;
console.log(fullName)
```

# 解构对象

使用对象析构，你可以从对象中提取属性，并将它们赋给 JavaScript 中的变量。对象析构还允许你在一条语句中提取许多属性，从嵌套对象中访问属性，如果属性不存在，设置一个默认值。

让我们析构下面的对象:

```
const employee = {name: "Kim",age: 24,department: {designation: "I am a software Dev "}}
```

解构后:

```
const employee = {name: "Kim",age: 24,department: {designation: "I am a software Dev "}}
const { name, age, department: { designation} } = employee;console.log(name);console.log(age);console.log(designation);
```

# 解构数组

可以从数组、对象和嵌套对象中获取数据，并使用析构赋值将数据赋给变量，析构赋值是一种 JavaScript 表达式，允许您将数组中的值或对象中的属性解包到不同的变量中。

```
let [firstName, middleName, lastName] = ['Peter', 'John', 'Parkar'];console.log(lastName)// Parkar
```

# 对象文字

在普通英语中，对象文字是用大括号括起来的逗号分隔的名称-值对列表。属性和函数可以用来表示这些值。

具有一个属性和一个函数的对象文本片段如下所示。

```
function studentAddress(address) {const {city, state} = address;const newStudentAddress = {city,state,country: 'India'};console.log(`${newStudentAddress.city}, ${newStudentAddress.state}, ${newStudentAddress.country}`)}studentAddress({city: 'Mumbai', state: 'Maharashtra'});
```

# For of 循环

for…of 命令产生一个循环，该循环遍历可迭代对象，如内置字符串、数组、类似数组的对象(如参数或节点列表)、TypedArray、Map、Set 和用户定义的可迭代对象。

让我们考虑下面的例子:

```
const students = [{ name: "Kim", city: "Dubai" },{ name: "Peter", city: "Paris"},{ name: "Kelvin", city: "Amsterdam" }];for( const student of students ) {console.log( student.name + " lives in " + student.city );}
//Kim lives in Dubai 
//Peter lives in Paris
//Kelvin lives in Amsterdam
```

# 扩展运算符

spread 操作符是 JavaScript ES6 操作符集中的新成员。它将一个 iterable(例如，一个数组)扩展成单独的元素。为了制作 JS 对象的浅层副本，spread 操作符被广泛使用。使用该运算符可以缩短并提高代码的可读性。

假设你要去商店买些杂货。所以你有一个购物清单数组，里面有你想买的所有东西。
现在你在商场里，你有一个篮子，里面装着你清单上的所有商品，但你想再添加一些。
向名为 shoppingBasket 的新数组中添加一些新产品，这将是 shoppingList 数组的副本。

```
const shoppingList = ["bread", "milk", "butter"];const shoppingBasket = [ ...shoppingList, "tofu", "pasta"];console.log(shoppingBasket);//bread, milk, butter, tofu, pasta
```

# Rest 运算符

我们可以使用 remainder 参数语法将不定数量的参数指定为一个数组。使用 rest 参数可以调用带有任意数量参数的函数，而不管它是如何定义的。在 ES2015 或 ES6 中，添加了 rest 参数，提高了处理参数的能力。

```
function add(...nums) {console.log(nums);}add(7, 8, 5, 4)//[7, 8, 5, 4]
```

# 默认参数

当没有为函数参数提供值时，默认参数用于设置默认值(即未定义)。如果函数中没有提供参数，它的值将变得不确定。编译器使用我们在这种情况下指定的默认值。

```
function foodShopping( food = "tomatoes") {console.log(`I'm going to buy ${food} from the shop`);}foodShopping();
```

# 让和 Const

事实证明，const 与 let 几乎相同。主要区别在于，一旦用 const 给变量赋值，就不能再给它重新赋值了。需要记住的是，用 let 声明的变量可以被重命名，而用 const 声明的变量则不能。

让我们看看 const 的例子

```
const originalPrice = 50;const percentOff = 20;const salePrice = originalPrice * (percentOff / 100);console.log(salePrice);
```

让我们看看 Let 的例子

```
let x = 15;
// Here x is 15

{
let x = 2;
// Here x is 2
}
```

# 进出口

JavaScript ES6 最重要的部分之一是导入和导出语句。它允许您将 JavaScript 类、方法、组件、常量和任何其他变量从一个 JavaScript 文件移动到另一个文件。

```
import { add } from './data.js';let result = add(6, 2);console.log(result);
//7
```

# 尾随逗号

从一开始，JavaScript 就支持数组文本中的尾部逗号，后来又将它们添加到对象文本、函数参数以及命名的导入和导出中。另一方面，JSON 不允许尾随逗号。

```
function add(param1,){const example = {name: 'Tom',};console.log(example)};add(2);//{name:"Tom"}
```

# 承诺

JavaScript 中的异步操作通过承诺来处理。当处理几个异步操作时，它们很容易管理，而回调会导致回调地狱和不可管理的代码。

让我们创建一个承诺，它返回一些用户数据，并在没有找到时抛出一个错误

```
const userData = new Promise((resolve, reject) => {const error = false;if(error) {reject('No found');} else {resolve({firstName: 'John',age: 32,email: 'johncreta@gmail.com'});}});userData.then((data) => console.log(data)).catch((error) => console.log(error));
```

# 取得

Fetch API 为您提供了一个 JavaScript 接口来访问和操作 HTTP 管道元素，如请求和响应。Fetch 还为定义其他与 HTTP 相关的概念(如 CORS 和 HTTP 扩展)提供了一个逻辑位置。

让我们考虑以下情况-

**获取第一个注释值**' https://jsonplaceholder . typicode . com/comments/1 '并记录其值。

**使用**‘https://jsonplaceholder . typicode . com/comments’发布新评论，并记录其值。

```
fetch('https://jsonplaceholder.typicode.com/comments/1').then((response) => response.json()).then((data) => console.log(data))fetch('https://jsonplaceholder.typicode.com/comments', {method: 'POST',body: JSON.stringify({name: 'Comment 109',email: 'johnpeter@gmail.com',body: 'Hello Buddy',postId: 1})}).then((response) => response.json()).then((data) => console.log(data))
```

# 异步和等待

开发 Async/Await 是为了使处理和编写链式承诺更加容易。异步函数返回一个承诺。如果函数返回一个错误，承诺将被拒绝。如果函数返回值，将解析承诺。

假设我想在控制台上使用 async 和 await 打印一个来自 Chuck Norris API 的随机笑话:

```
const apiUrl = "https://api.chucknorris.io/jokes/random";async function getJoke() {const response = await fetch(apiUrl);const data = await response.json();console.log(data.value);}getJoke();
```

你可以在这里了解更多关于 ES6 [的信息](https://developer.mozilla.org/en-US/docs/Web/JavaScript)

你也可以在这里了解 NodeJS 集成