# 学习 Go Lang Days 27 —在 Go Lang #100daysofcode 中找出一个单词是否是变位词

> 原文：<https://medium.com/codex/learning-go-lang-days-27-find-out-if-a-word-is-a-anagram-in-go-lang-100daysofcode-c369d9fe7982?source=collection_archive---------0----------------------->

## [抄本](http://medium.com/codex)

昨天我一直在看如何在 go lang 中编写 Fizz Buzz 的代码，我想我会继续讨论典型的代码测试问题，今天我会编写一个 Go Lang 方法，该方法采用两个单词并返回它们是否是彼此的变位词。

要做到这一点，我们首先需要了解什么是变位词。如果一个单词包含所有相同的字母，那么它就是另一个单词的变位词。例如`listen`是`silent`的变位词。