# 是什么让编程语言过于复杂？

> 原文：<https://medium.com/codex/what-makes-a-programming-language-too-complicated-1f64e0172ac2?source=collection_archive---------1----------------------->

![](img/276db979f1a8181db2d4feb4a4a3c399.png)

由[杰佛森·桑多斯](https://unsplash.com/@jefflssantos?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

编程语言最重要的一个方面是它的复杂性。太简单的语言通常被认为是不表达的和冗长的，而太复杂的语言很难学习并导致难以理解的代码库。因此，编程语言设计者最终不得不对复杂性采取立场。例如，简单性可以说是 Go 最重要的特性，而 C++似乎增加了几乎所有流行的语言特性。

一种编程语言的感知复杂性不仅仅是它有多少种语言特性或者有多少种方法来完成同样的任务的函数。在这两个指标中，Rust 接近 C++，但语言复杂性并不是 Rust 的主要流行批评之一。虽然 Rust 通常被批评为难以学习和编写缓慢(这都是其内存管理模型的副作用)，但大多数对 C++的批评都与功能过多或某个特定功能使程序不太清晰有关。Python 和 Rust 一样，是一种非常有特色的语言，通常不认为它很复杂。这部分是因为它的目标用例；低级程序员比大多数 Python 开发人员更有理由关心简单性。然而，不考虑社区，Python 也因为其语言设计比 C++更复杂而受到更少的批评。

Rust 和 Python 的复杂性比 C++的危害更小，因为新特性的实现要小心得多，所以即使有多种方法来完成同一任务，其中一种方法显然更好。Rust 尤其迫使开发者走上一条统一的道路，使用自己的模型。

从[习题](https://exercism.io)中抽取以下问题:

> 变位词是重新排列字母以形成一个新单词。给定一个单词和一个候选单词列表，选择给定单词的字谜子列表。
> 
> *给定“listen”和类似“enlists”“Google”“inlets”“banana”的候选列表，程序应该返回一个包含“inlets”的列表。*

最新的 C++解决方案(只有相关的方法和一个助手)是:

```
vector<string> anagram::matches (vector<string> candidates)
{
    vector<string> res;

    for (auto& candidate: candidates)
    {
        string candidate_lower = candidate;
        std::transform (candidate.begin(), candidate.end(), candidate_lower.begin(), ::tolower);
        if(match(candidate_lower)) {
            res.push_back(candidate);
        }
    }

    return res;
}
bool anagram::match (std::string word){
    return is_permutation(input.begin(),input.end(),word.begin()) && word.size() == length && word != input;
}
```

在我看来，这是相当合理的，即使你是 C++新手，这也是很容易理解的。

以下是顶级解决方案的相关方法(`is_anagram`与上面的`anagram::match`非常相似)

```
std::vector<std::string> matches(std::vector<std::string> candidates) {
    std::vector<std::string> matched;
    std::copy_if(std::begin(candidates), std::end(candidates), std::back_inserter(matched), [this](auto candidate) { 
            std::string loweredCandidate;
            std::transform(std::begin(candidate), std::end(candidate), std::back_inserter(loweredCandidate), ::tolower);
            return is_anagram(this->m_str, loweredCandidate); 
    });
    return matched;
}
```

它简短，简洁，难以理解。不过，我相当肯定作者不会用实际的代码库来写这篇文章。和往常一样，一旦理解了代码的各个部分，就很容易理解发生了什么，但是 C++笨拙的迭代器和 lambda 实现意味着大多数 C++开发人员避免使用它们，因此不能很快理解这些代码。这个问题的许多 C++解决方案只对`std::is_permutation`使用迭代器，很少使用匿名函数。

针对同一问题的顶级 Rust 解决方案是:

```
pub fn anagrams_for<'a>(word: &str, candidates: &'a [&'a str]) -> Vec<&'a str> {
  let word = word.to_lowercase();
  let sorted_word = sort(&word);
  candidates
    .iter()
    .cloned()
    .filter(|&candidate| {
      let can = candidate.to_lowercase();
      sort(&can) == sorted_word && can != word
    })
    .collect::<Vec<&'a str>>()
}
```

这个解决方案也使用迭代器/匿名函数，我点击的五六个解决方案中的每一个都是如此。迭代器和闭包在 Rust 中非常符合人体工程学；它们易于阅读，使得处理借用检查器有时更容易，而且最重要的是，迭代器通常比普通的循环更快。

针对 is_anagram(移除了注释)的顶级 Python 解决方案是:

```
def detect_anagrams(original, candidates):
	return [candidate for candidate in candidates if is_an_anagram(original, candidate)]

def is_an_anagram(original, candidate):
	original = original.lower()
	candidate = candidate.lower()
	return original != candidate and sorted(original) == sorted(candidate)
```

几乎每个 Python 解决方案都使用相同的过程。当然，Python 隐藏了大部分复杂性。

这些`is_anagram`解决方案展示了每种语言的一些重要方面。虽然 C++有一个简短的方法来解决这个问题，但是几乎没有人使用理解它所需要的特性，即迭代器。出于这个原因，大多数 C++开发人员会选择主要使用普通的 For 循环来解决 is_anagram。

另一方面，Rust 也有迭代器，几乎每个人都用它们来解决问题。Python 有更多的方法来解决问题，但几乎每个人都只是使用列表理解。

另一方面，有像 C 和 Go 这样的语言，它们强调简单胜过其他任何东西。围棋只有一种解决问题的方法；要做出一个对其他围棋开发者来说不透明的解决方案，需要进行有意的混淆。

让一门编程语言变得太复杂的不是它拥有的功能数量，而是这种语言拥有但人们没有使用的功能数量。

> *在 C++中，有一种更小、更简洁的语言正在努力发展。——比雅尼·斯特劳斯特鲁普，C++的创始人*

*原载于 2020 年 8 月 4 日*[*https://mkhan 45 . github . io*](https://mkhan45.github.io/2020/08/04/What-makes-a-programming-language-too-complicated.html)*。*