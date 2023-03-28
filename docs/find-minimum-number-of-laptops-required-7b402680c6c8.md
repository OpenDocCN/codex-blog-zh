# 查找所需笔记本电脑的最小数量

> 原文：<https://medium.com/codex/find-minimum-number-of-laptops-required-7b402680c6c8?source=collection_archive---------4----------------------->

## 今日 GFG 问题[2022 年 12 月 31 日]

![](img/5b3190ba632c1bb15088fb2f574dce31.png)

照片由[亚历山大·巴甫洛夫·波德瓦尔尼](https://unsplash.com/@freestockpro?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

有 **N 个**任务，任务的开始和结束时间分别在数组 **start[]** 和 **end[]** 中给出。每项工作需要一台笔记本电脑，笔记本电脑不能共享。考虑到你可以在不工作的时候把你的笔记本电脑给别人，找出你需要的最小数量的笔记本电脑。

**例 1:**

```
Input:
N = 3
start[] = {1, 2, 3}
end[] = {4, 4, 6}
Output:
3
Explanation:
We can clearly see that everyone's supposed to
be doing their job at time 3\. So, 3 laptops
will be required at minimum.
```

**例 2:**

```
Input:
N = 3
start[] = {1, 5, 2}
end[] = {2, 6, 3}
Output :
1
Explanation:
All jobs can be done using 1 laptop only.
```

**你的任务:**
你不需要读取输入或者打印任何东西。您的任务是完成函数 **minLaptops()** ，该函数采用整数 N 和两个数组 start 和 end 来表示 N 个作业的开始和结束时间，并返回所需的最少笔记本电脑。

**期望时间复杂度:** O(N*logN)
**期望辅助空间:** O(N)

# 解决方案#1

让我们举一个例子

N = 3
开始[] = {1，2，3}
结束[] = {4，4，6}
工作开始于 1。(笔记本电脑 1)

两点开始工作。(笔记本电脑 2)

工作从 3 点开始。(笔记本电脑 3)

工作在 4 点结束(笔记本电脑 1 现在是免费的)。

工作在 4 点结束(笔记本电脑 2 现在是免费的)。

工作在 6 点结束(笔记本电脑 3 现在是免费的)。

我们可以观察到，每次我们开始工作时，我们需要一台计算机，每次我们结束工作时，我们释放计算机。所以基本上我们可以保留一个计数器，每次我们遇到开始，我们增加计算机的数量，每次我们遇到结束，我们减少计算机的数量。对于每个状态，我们可以维护一个变量来跟踪 max。一次需要的电脑。

对于这种方法，我们应该有一个排序的开始时间和结束时间，这样我们就可以选择哪个是最小的。

```
class Solution {
    public int minLaptops(int n, int[] start, int[] end) {
        Arrays.sort(start);
        Arrays.sort(end);
        int total = 0;
        int max = 0;
        int i = 0;
        int j = 0;
        while(i < n && j < n) {
            if(start[i] < end[j]) {
                i++;
                total++;
            } else {
                j++;
                total--;
            }
            max = Math.max(max, total);
        }
        return max;
    }
}
```

时间复杂度:O(NlogN)

空间复杂度:O(1)

# 结论

你可以看到这个问题有很多种形式，但是解决这个问题的思路是一样的。

加入我的电报频道，获取所有 GFG POD 解决方案。

 [## 唯一主|编码

### 编码#编程# leet code # code chef # hacker rank # interview bit # gfg

t.me](https://t.me/sole_master_coding) 

**谢谢大家！！！**

**新年快乐#2023**