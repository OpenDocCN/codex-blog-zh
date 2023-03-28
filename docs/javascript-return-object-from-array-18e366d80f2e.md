# JavaScript —从数组返回对象

> 原文：<https://medium.com/codex/javascript-return-object-from-array-18e366d80f2e?source=collection_archive---------6----------------------->

## [法典](http://medium.com/codex)

![](img/f6400c0b62a06dc04a55397165ef2132.png)

让我们看看下面的练习，学习如何处理数组中的元素并返回一个新的对象。

> ***Q .按照下面的说明创建*** `***getData***` ***函数。***
> 
> `***getData*` *函数获取三个参数。***
> 
> `*salesArr*` *:每天销售额
> ex)* `*[["20190401", 34], ["20190402", 23], ["20190403", 29]]*`
> 
> `*reviewArr*` *:每天评论量
> ex)* `*[["20190328", 3], ["20190401", 0], ["20190403", 1]]*`
> 
> `*likeArr*` *:每天点赞量
> ex)* `*[["20190328", 98], ["20190401", 102], ["20190403", 125]]*`
> 
> *以上数组仅为示例，这意味着元素的数量可以远远多于 3 个。*
> 
> *为什么要把数组改成对象？因为如果我们想要检查每天的销售、评论和喜欢的数量，我们需要迭代每个数组，这意味着数据结构不方便或不高效。*
> 
> *返回的对象应该有以下三个属性。*
> 
> `*sumAmount*` *:总销售额*
> 
> `*sumReview*` *:总点评*
> 
> `*sumLike*` *:总点赞*

**答:我的解决方案是:**

```
function getData(salesArr,reviewArr,likeArr){

let sumData = function (arr) {
let total = 0;
for (let i = 0; i<arr.length; i++){
 total += arr[i][1];
}
 return total;
}let result = {
 sumAmount: sumData(salesArr),
 sumReview: sumData(reviewArr),
 sumLike: sumData(likeArr)
};return result;};
```

1.  首先，我想了想当函数被执行时，我们需要得到什么结果。由于该函数获得三个数组作为参数，我们可以使用这些数组中的元素数据，并需要返回一个新的对象，其属性为销售、评论和喜欢的总数。
2.  让我们看看数组的结构。每个数组将每个元素作为另一个数组，数组内的数组包含日期和数字元素。
3.  为了得到销售、评论和属性的总数，我们知道需要对数组 : `[["20190401", **34**], ["20190402", **23**], ["20190403", **29**]] // bolded numbers should be summed up.`的每个元素数组的第二个元素**求和**
4.  因为我们需要对数组的每个元素都这样做，所以看起来我们需要一个带有`for`循环的函数`sumData`:

```
let sumData = function (arr) {
let total = 0;
for (let i = 0; i<arr.length; i++){
 total += arr[i][1];
}
 return total;
}
```

我们可以通过`arr[i][1]`(每个元素数组中的第二个元素)访问#3 中提到的元素。变量`total`最初被声明为 0，它将随着我们的迭代而递增。

5.

```
let result = {
 sumAmount: sumData(salesArr),
 sumReview: sumData(reviewArr),
 sumLike: sumData(likeArr)
};
```

现在我们有了获得销售、评论和喜欢总数的函数，让我们将它们设置为一个新对象`result`的属性。

属性名称(键)写在`:`的左边。属性值(value)在右边，我们可以通过为每个数组调用函数`sumData`来获得它(例如。`sumData(salesArr)`)。