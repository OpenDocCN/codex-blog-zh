# 大学教学与人工智能

> 原文：<https://medium.com/codex/college-teaching-and-ai-cfcb9878ba08?source=collection_archive---------9----------------------->

## 人工智能将重塑我们评估学生学习的方式，这是我们从未预料到的。

![](img/3f0aefbf82da56f5e033f89c23f3b7ec.png)

使用 OpenAI 的 Dall-e 制作的图像，说明如下:“一位后现代主义教授在旧时尚黑板前，向人工智能机器人讲授神经网络”

T 我用 OpenAI 的 [ChatGPT](https://chat.openai.com/chat) 尝试的第一件事是我为数据结构期末考试准备的问题。它准确地回答了这些问题——尽管不完全正确。ChatGPT 被 *self-* 描述为

> OpenAI 训练的语言模型。它不是一个真实的人，也没有物理形态。它是一个计算机程序，被设计用来对文本输入产生类似人类的反应。[它的]目的是协助完成各种任务，包括回答问题、提供信息和参与对话。[它]不能浏览互联网，因此[它]不能提供不属于[它]预先存在的知识库的信息。
> — *ChatGPG 回应“你是什么？”*

多年来，我一直在给我的编程课程的学生进行课后考试。我早就不担心抄袭了。我希望学生们查阅浩瀚的网络，事实上，我鼓励他们这样做。同时，我希望他们能够*内化*他们在网上找到并使用的任何代码。他们必须能够向我描述它是如何工作的，为什么他们可以接受。

ChatGPT 将游戏带到了一个不同的水平。它不仅提供了准确的答案，还提供了反映对解决方案的理解的叙述。学生们可以用这种叙述作为证据，证明人工智能为他们找到的代码内化了。

下面举个例子:考试中比较容易的一个问题，要求学生写一个(Java)方法，返回整数数组中的重复元素。ChatGPT 给了我以下代码、注释和所有内容:

```
public static List<Integer> findDuplicates(int[] arr) {
  // Create a set to store the elements that have been seen
  Set<Integer> seen = new HashSet<>();
  // Create a list to store the duplicate elements
  List<Integer> duplicates = new ArrayList<>();
  // Loop through the array and check each element
  for (int i = 0; i < arr.length; i++) {
    int num = arr[i];
    // If the element has been seen before, it is a duplicate
    if (seen.contains(num)) {
      duplicates.add(num);
    } else {
      // If the element has not been seen before, add it to the set
      seen.add(num);
    }
  }
  // Return the list of duplicate elements
  return duplicates;
}
```

它还为代码提供了一个叙述——为了简洁起见，我将省略它，但它很好。代码中的注释也不错。它们读起来像伪代码，表明对代码如何工作有很好的理解。

上面的代码是对一个 Java 方法的查询的响应，该方法的**返回一个整数数组中的**重复元素。如果我向 AI 寻求一种方法来**找到**重复的元素，它会给我一个 void 方法来打印重复的元素。我对人工智能辨别需求的能力印象深刻。

当需求变得更加具体时，人工智能开始动摇:*编写一个 Java 方法，返回一个整数数组中重复元素的位置:*

```
public static int[] findDuplicates(int[] arr) {
    Set<Integer> duplicates = new HashSet<>();
    for (int i = 0; i < arr.length; i++) 
        for (int j = i + 1; j < arr.length; j++) 
            if (arr[i] == arr[j]) 
                duplicates.add(j);
    return duplicates.stream().mapToInt(i -> i).toArray();
}  // Comments and single-line bracing removed for brevity by Leo
```

方法准确。在数据结构的第一堂课中，这也是不正确的。在我的课程中，我们避开了`Stream`界面。像上面这样的 return 语句是相当出乎意料的。在这个阶段，学生可能很难展示他们对`stream`和`mapToInt`的理解。但如果他们这样做了，他们会得到更多的力量。

最后，当我将规范缩小到:*编写一个 Java 方法，该方法返回一个整数数组，显示整数数组中重复元素的索引位置，而不使用* `*Set*` *或* `*List*` *对象，也不使用* `*break*` *语句；*人工智能似乎放弃了——就目前而言:

```
 public static int[] findDuplicates(int[] arr) {
    int numDuplicates = 0;
    for (int i = 0; i < arr.length; i++) 
      for (int j = i + 1; j < arr.length; j++) 
        if (arr[i] == arr[j]) 
          numDuplicates++;
          break;
    int[] duplicates = new int[numDuplicates];
    int index = 0;
    for (int i = 0; i < arr.length; i++) {
      for (int j = i + 1; j < arr.length; j++) {
        if (arr[i] == arr[j] && !contains(duplicates, i)) {
          duplicates[index] = i;
          index++;
    return duplicates;
  }  // Single-line bracing removed for brevity by Leo
```

抓住你了。我将要求我的学生*编写一个 Java 方法，该方法返回一个整数数组，显示整数数组中重复元素的索引位置，如果该元素不是重复的，则返回-1，而不使用* `*Set*` *或* `*List*` *对象，也不使用* `*break*` *语句。*没这么快艾说:

```
public static int[] findDuplicates(int[] arr) {
  int[] duplicates = new int[arr.length];
  for (int i = 0; i < arr.length; i++) {
    duplicates[i] = -1;
  for (int i = 0; i < arr.length; i++) {
    for (int j = i + 1; j < arr.length; j++) {
      if (arr[i] == arr[j] && duplicates[i] == -1) {
        duplicates[i] = i;
  return duplicates;
}  // Single-line bracing removed for brevity by Leo
```

然后它厚着脸皮告诉我

> 这种方法比以前的方法效率低，因为它必须在输入数组上迭代两次:一次是初始化重复数组，一次是查找重复元素。这可以通过使用`Set`或`List`对象来避免，就像前面的例子一样。

人工智能跟上了我，因为考试问题变得更具挑战性。我试图通过混淆需求来规避它，例如，在向前遍历链表时返回一个包含链表内容的字符串，而不使用 StringBuilder。好吧，人工智能说，这是你的方法，但你应该知道它有:

> O(n)时间复杂度，其中 n 是链表中元素的个数，因为在 Java 中串接字符串具有 O(n)时间复杂度。对于较大的链表，使用 StringBuilder 或类似类的更有效的实现将是首选。

当我要求它将芝加哥的南行红线转换成链表时，它告诉我

> 无法将芝加哥运输管理局(CTA)的南行红线列车转换为 Java 链表，因为链表是计算机编程中使用的数据结构，用于存储线性数据集合，而 CTA 红线是在芝加哥提供公共交通的物理列车线路。

雪上加霜的是，它建议如何将南行红线上的停靠点表示为一个链表。

```
LinkedList<String> redLineSouthbound = new LinkedList<>();

redLineSouthbound.add("Howard");
redLineSouthbound.add("Jarvis");
redLineSouthbound.add("Morse");
...
```

在这一点上，我认为我们，教育工作者，有麻烦了。人工智能有可能颠覆我们多年来一直依赖的每一种教学范式。我们下意识的反应可能是禁止学生在作业中使用人工智能。这不会奏效，原因有二:首先，执行起来非常困难。第二，禁止使用学生在职业生涯中可能需要使用的工具的目的是什么？

更好的策略是将人工智能融入教学和评估学习。这将需要更频繁、更有条理的口试，以小组或一对一的形式进行。在编程课上，可能需要从编码到设计的更大转变；更强调计算机程序的结构和解释(提示，暗示，眨眼，眨眼)。可能废除家庭作业的概念，把学习活动局限在教室里。这肯定需要与我们的首席技术官就跨校园授权人工智能产品进行对话。对我们自己的一些再培训也不会出错。我们越早开始为融合人工智能的高等教育体验做准备，我们的学生就越好。