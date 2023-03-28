# 技术挑战:非重复数据收集

> 原文：<https://medium.com/codex/technical-challenge-the-non-dup-collection-92f1cb261d0a?source=collection_archive---------19----------------------->

## *招聘人员，说真的，请停止…*

在过去的几个月里，我的 LinkedIn 个人资料收到了太多的工作邀请，有些真的很有吸引力(主要是那些考虑为非智利公司工作，月收入以美元计)，其他的只是超出范围。

为了记录在案，如果你的读者中有人是招聘人员，请阅读候选人的简历，并看看他们简介中的链接。这通常能很好地反映候选人的专业知识。
根据我们的经验，收到要求一些专业知识的邀请真的很烦人。

![](img/4f9e4939e70a578344cf26134a9478df.png)

叠树一直是一项挑战——由 Michał Parzuchowski 在 Unsplash 上[拍摄](https://unsplash.com/@mparzuchowski?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

在不止一个这样的提议中，涉及到了一种技术挑战，可悲的是，大多数情况下，所有的问题和挑战都来自于“书本”。我所说的“*这本书*”是指在大多数文章、论坛帖子甚至一些出版的书籍中发现的典型的编码挑战。

怀着和每个招聘人员上床的心情，我将回答“*非重复收集挑战*”这个同样的编码挑战。

让我们假设你有一个简单的对象集合。他们中的一些人不断重复。您需要从这个集合中获取唯一的值，还需要获取哪个元素的重复次数最多。

我至少见过这个问题的两种不同版本。

1.  输入不是一个集合，而是一个字符串，其中每个字母至少可以重复一次；例如`"aaaaaaaabbbccddddcccceeee"`
2.  输入是一个字符串数组，例如公司名称
    `["Google", "Apple", "Apple", "Microsoft", "Facebook", "Google", "Apple", "Facebook", "Apple", "Microsoft", "Microsoft", "Google"]`

其他变体可以考虑使用更复杂对象的集合来代替，但是逻辑保持不变。第二种情况通常隐藏在以下内容之后:

> 我们公司有几个项目，每个项目都与一个特定的客户相关联。我们有一个代表我们所有项目和所有客户的数组。你的任务是获得所有独特客户的列表，并告诉我们哪个客户拥有我们开发的最多项目。”

如你所见，问题是一样的:

*   某种集合*(字符串是字符的集合(单字符字符串))*
*   获取唯一值
*   获取具有最大重复次数的项目…

*易*“…

因此，让我们为此建立一个解决方案。我们将使用案例 1，并且我们将用 Typescript 编码:

```
// Note: This is one possible solution. You can get as creative as you want.
// Step by step ... (uuuuh baby !) <- NKOTB someone? :P
// 1\. Get original collection 
// 2\. Dedup the values
// 1\. & 2\. can be joined in teh same function
function dedupeStringSet(data: string): string {
  // result is initialized with the input string just to have something to return when testing without any code
  let result: string = data;
  // As the string is not a collection (per se) turn it into  a char Array
  const dataArr: Array<string> = data.split('');
  // Sets a a "new" (not that new) datatype, also a collection but it doesn't admit any duplicates. 
  //Google for *javascript unique values* and it's one of the first results
  const dataSet = new Set(dataArr);
  // Turn the Set again into an Array (using Array.from) and then to a string
  result = Array.from(dataSet).join(''); // We're ready so return
  return result;
}
// 3\. Get max repeated item
// I see a problem here. This deduping strategy gives no information on how many repetitions per char
```

由于我们的第一次尝试未能获得最大值，我们将尝试另一种方法。我将在代码的不同部分添加一些注释。

```
// The object is declared outside the function so we can use it in any other place we require (for eg. the getMax function)
// Every char/letter on the string has a counter
let cntObj: { [key: string]: number } = {}
function dedupStringObj(data: string): string {
  // Initialize the object on every run 
  cntObj = {};
  let result: string = data;
  const dataArr: Array<string> = data.split('');
  // We'll iterate through all chars  in the string
  for (let index = 0; index < dataArr.length; index++) {
    // This would be a char
    const element    = dataArr[index];
    // If the char does exist as an attribute/key in the object the add 1, else first value is 1
    cntObj[element] = (cntObj[element]) ? cntObj[element]+1 : 1;       
  }
  result = (Object.keys(cntObj)).join('');

  return result;
}// 3\. Get max repeated item
/**
 * Get the most repeated char in a non-unique char string
 * @param dataObj JSON object used as a dictionary where each char stores its repetition counter
 * @returns the string corresponding to the most repeated item
 */
function mostRepeatedObj(dataObj: { [key: string]: number }): string {
  // Initializes the key and maxValue to have a starting pivot
  let maxValue: number = 0;
  let maxKey: string   = '';
  // Gets all object keys in an array so we can iterate through them and get the most repeated item
  const keys: Array<string> = Object.keys(dataObj);
  for (let index = 0; index < keys.length; index++) {
    // Get the key and counter value for that key
    const key: string       = keys[index];
    const currValue: number = dataObj[key];
    // Typical max value strategy, currValue replaces maxValue when >
    // If this happens also keep track of the key (which would be the most repeated item)
    if (currValue >= maxValue) {
      maxValue = currValue;
      maxKey   = key;
    }         
  } return maxKey;
}
```

我们还需要一些代码来进行测试运行:

```
const input = 'aaaaabbbcccccceeeccccdd';
console.log(input, '\n');let dedup = dedupStringSet(input);
console.log(dedup, '\n');dedup = dedupStringObj(input);
console.log(dedup);
console.log(cntObj);const maxObj: string = mostRepeatedObj(cntObj);
console.log(maxObj, '\n');/**
aaaaabbbcccccceeeccccdd <- inputabced <- dedupStringSetabced <- dedupStringObj
{ a: 5, b: 3, c: 10, e: 3, d: 2 } <- cntObj
c <- mostRepeatedObj
**/
```

来运行这个程序。

1.  将它存储在一个单独的文件夹中，在一个名为`<whatever you want>.ts`的文件中(替换成你想要的:P)
2.  运行`npm install typescript ts-node @types/node`
3.  跑`./node_modules/.bin/ts-node <whatever you want>.ts`

但是…性能可能是一个问题。我希望您已经阅读过它(如果没有，您现在正在做:)，使用 JSON 对象作为 hashmap/dictionary 可能会很慢，因为您必须检查对象中的每个属性是否都存在。那将会是这条特别的线:

`cntObj[element] = (cntObj[element]) ? cntObj[element]+1 : 1;`

所以让我们尝试一个使用 Map 对象而不是 JSON 对象的解决方案。

```
// Almost the same as the object version, but using a Map
let cntMap: Map<string, number> = new Map<string, number>();
function dedupStringMap(data: string): string {
  cntMap = new Map<string, number>();
  let result: string = data;
  const dataArr: Array<string> = data.split('');
  for (let index = 0; index < dataArr.length; index++) {
    const element = dataArr[index];
    // Checks if the char exist as a key, if it does double check when getting (??0 else the Typescript trasnpiler will throw an error)
    // and increment. If it doesn exist then first value is 1.
    const inc: number = (cntMap.has(element)) ? (cntMap.get(element)??0) + 1 : 1;    
    // Store the value in the map  
    cntMap.set(element, inc);
  }
  // As Sets, Maps do not admit duplicates, so take the key and turn the iterator into an array. Then 
  // turn the Array back into a string.
  result = Array.from(cntMap.keys()).join('');

  return result;
}function mostRepeatedMap(dataMap: Map<string, number>): string {
  let maxValue: number = 0;
  let maxKey: string   = '';
  const keys: Array<string> = Array.from(dataMap.keys());
  for (let index = 0; index < keys.length; index++) {
    const key: string       = keys[index];
    const currValue: number = dataMap.get(key) || 0;
    if (currValue >= maxValue) {
      maxValue = currValue;
      maxKey   = key;
    }         
  }
  return maxKey;
}
```

仅与对象版本略有不同。要运行:

```
let dedup = dedupStringMap(input);
console.log(dedup);
console.log(cntMap);const maxMap: string = mostRepeatedMap(cntMap);
console.log(maxMap, '\n');/**
aaaaabbbcccccceeeccccdd abced
Map(5) { 'a' => 5, 'b' => 3, 'c' => 10, 'e' => 3, 'd' => 2 }
c 
**/
```

好了，我们有 3 种方法对一个字符串(或者一个集合，如果你适当地调整代码)进行非重复数据删除，还有 2 种方法从结果对象中获得最大值。

# 性能呢？

让我们挑选所有的代码并混合起来，添加一些时间测量信息(希望你不介意增量复制和粘贴):

```
function dedupStringSet(data: string): string {
  let result: string = data;
  const dataArr: Array<string> = data.split('');
  const dataSet = new Set(dataArr);
  result = Array.from(dataSet).join('');
  return result;
}let cntObj: { [key: string]: number } = {}
function dedupStringObj(data: string): string {
  cntObj = {};
  let result: string = data;
  const dataArr: Array<string> = data.split('');
  for (let index = 0; index < dataArr.length; index++) {
    const element = dataArr[index];
    cntObj[element] = (cntObj[element]) ? cntObj[element]+1 : 1;       
  }
  result = (Object.keys(cntObj)).join('');

  return result;
}// Almost the same as the object version, but using a Map
let cntMap: Map<string, number> = new Map<string, number>();
function dedupStringMap(data: string): string {
  cntMap = new Map<string, number>();
  let result: string = data;
  const dataArr: Array<string> = data.split('');
  for (let index = 0; index < dataArr.length; index++) {
    const element = dataArr[index];
    // Checks if the char exist as a key, if it does double check when getting (??0 else the Typescript trasnpiler will throw an error)
    // and increment. If it doesn exist then first value is 1.
    const inc: number = (cntMap.has(element)) ? (cntMap.get(element)??0) + 1 : 1;    
    // Store the value in the map  
    cntMap.set(element, inc);
  }
  // As Sets, Maps do not admit duplicates, so take the key and turn the iterator into an array. Then 
  // turn the Array back into a string.
  result = Array.from(cntMap.keys()).join('');

  return result;
}/**
 * Get the most repeated char in a non-unique char string
 * @param dataObj JSON object used as a dictionary where each char stores its repetition counter
 * @returns the string corresponding to the most repeated item
 */
function mostRepeatedObj(dataObj: { [key: string]: number }): string {
  // Initializes the key and maxValue to have a starting pivot
  let maxValue: number = 0;
  let maxKey: string   = '';
  // Gets all object keys in an array so we can iterate through them and get the most repeated item
  const keys: Array<string> = Object.keys(dataObj);
  for (let index = 0; index < keys.length; index++) {
    // Get the key and counter value for that key
    const key: string       = keys[index];
    const currValue: number = dataObj[key];
    // Typical max value strategy, currValue replaces maxValue when >
    // If this happens also keep track of the key (which would be the most repeated item)
    if (currValue >= maxValue) {
      maxValue = currValue;
      maxKey   = key;
    }         
  } return maxKey;
}function mostRepeatedMap(dataMap: Map<string, number>): string {
  let maxValue: number = 0;
  let maxKey: string   = '';
  const keys: Array<string> = Array.from(dataMap.keys());
  for (let index = 0; index < keys.length; index++) {
    const key: string       = keys[index];
    const currValue: number = dataMap.get(key)??0;
    if (currValue >= maxValue) {
      maxValue = currValue;
      maxKey   = key;
    }         
  }
  return maxKey;
}const input = 'aaaaabbbcccccceeeccccdd';
console.log(input, '\n');console.time('set');
let dedup = dedupStringSet(input);
console.timeEnd('set');
console.log(dedup, '\n');console.time('obj');
dedup = dedupStringObj(input);
console.timeEnd('obj');
console.log(dedup);
console.log(cntObj);console.time('maxObj');
const maxObj: string = mostRepeatedObj(cntObj);
console.timeEnd('maxObj');
console.log(maxObj, '\n');console.time('map');
dedup = dedupStringMap(input);
console.timeEnd('map');
console.log(dedup);
console.log(cntMap);console.time('maxMap');
const maxMap: string = mostRepeatedMap(cntMap);
console.timeEnd('maxMap');
console.log(maxMap, '\n');/**
aaaaabbbcccccceeeccccdd set: 0.108ms
abced obj: 0.08ms
abced
{ a: 5, b: 3, c: 10, e: 3, d: 2 }
maxObj: 0.045ms
c map: 0.076ms
abced
Map(5) { 'a' => 5, 'b' => 3, 'c' => 10, 'e' => 3, 'd' => 2 }
maxMap: 0.079ms
c 
**/
```

你可以根据结果做出自己的结论。
最后，在我写这篇文章的时候，我有了另一个想法:*如果集合是降序排列的，并且第一项被取为最大值，那么重复次数最多的函数的性能会提高吗？*

我将把它作为一个有趣的编码练习留给读者(也许比本文的挑战好得多)。这将需要重写某些函数中的一些行，并且很可能改变 cntObj 的类型(我在考虑一个*数组< { key: string，counter: number } >)。* 如果你想以编码为乐，以下几个链接会有所帮助:

*   [https://developer . Mozilla . org/es/docs/Web/JavaScript/Reference/Global _ Objects/Array/sort](https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Global_Objects/Array/sort)
*   [https://stack overflow . com/questions/37982476/how-to-sort-a-map-by-value-in-JavaScript](https://stackoverflow.com/questions/37982476/how-to-sort-a-map-by-value-in-javascript)(查看 [Miroslav Savovski](https://stackoverflow.com/users/8494762/miroslav-savovski) 的回答)

因此，如果你在面试中遇到这个问题(或类似问题)的技术挑战，我希望你能记住这篇文章。

所以招聘人员，请停止。一个需要记忆任何库或包方法签名的技术编码挑战，或者诸如从底部到顶部遍历二叉树的复杂算法，将不会精确地评估候选人是否能够覆盖工作职位，也不会评估他们是否很适合组织。最坏的情况是，大多数公司已经考虑了试用期(并且已经考虑了所有相关成本)，在此期间，管理层决定雇佣候选人是否是一个好的选择。

有更好的方法来雇佣高水平的开发者。
保持安全。