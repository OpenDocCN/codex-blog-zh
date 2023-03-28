# Julia 和 Python——两者都用！

> 原文：<https://medium.com/codex/julia-and-python-use-both-e61a2e7847e0?source=collection_archive---------13----------------------->

最近有很多文章比较 Julia 和 Python。最普遍的分析是 Julia 是更好的语言，但是 Python 生态系统要成熟得多。我同意这两种说法，但这是一个错误的选择——两者都用。

在这篇文章中，我想支持我最喜欢的开发环境:用 Julia 编程并调用 Python 库。这使您能够访问 Python 生态系统中的所有精彩价值，同时编写运行速度提高 50 倍的代码。更快的 Julia 代码允许一个人在一个良好的类似 Python 的生产环境中编写 CPU 密集型代码，而不是开发 Cython 或 C 库所需的痛苦和时间。

这是我目前工作中的一个例子。我正在为语音识别开发新的语言模型和解码器技术。该代码在深度学习模型的输出上运行。我不想开发一个全新的语音识别器，只是最后阶段。然而，解码器必须运行得很快——我的代码正在对来自深度学习模型的逻辑进行大量搜索。因此，我在识别器的第一阶段使用 Pytorch 和 HuggingFace 模型，并用 Julia 编写解码器。

下面是我访问 PyTorch 和 Huggingface 的 Julia 代码。

```
using PyCallstruct HuggingFace
    torch::PyCall.PyObject
    transformers::PyCall.PyObject
    datasets::PyCall.PyObject
    soundfile::PyCall.PyObject function HuggingFace()
        torch = PyCall.pyimport(“torch”)
        transformers = PyCall.pyimport(“transformers”)
        datasets = PyCall.pyimport(“datasets”)
        soundfile = PyCall.pyimport(“soundfile”)
        new(torch,transformers,datasets,soundfile)
    end
end
```

请注意 Julia 与 Python 的交互是多么容易。访问 PyTorch 模块非常简单

```
torch = PyCall.pyimport(“torch”)
```

在这段代码中，我将所有 Python 和 HuggingFace 信息存储在一个 HuggingFace 结构中。在代码之外，它实际运行 Python 和 C 的事实是完全隐藏的。一切都只是一个正常的 Julia 值。

下面是一个使用 HuggingFace 结构的方法:

```
load_model(hf::HuggingFace,
           model_name=”facebook/wav2vec2-base-960h”)= 
    hf.transformers.Wav2Vec2CTCTokenizer.from_pretrained(model_name)
```

HuggingFace struct (hf)包含对 HuggingFace transformers 模块的引用，该模块包含带有 from_pretrained 方法的 wav 2 vec 2 tctokenizer 类。返回的 PyTorch 模型对象从 Julia 代码的角度来看只是一个 Julia 值。

这里还有一些 Julia 调用 Python 写的 Pytorch 代码的例子

```
load_dataset(hf::HuggingFace,dataset_name=”timit_asr”) = hf.datasets.load_dataset(dataset_name)device(hf::HuggingFace) = if hf.torch.cuda.is_available() hf.torch.device(“cuda:0”) else “cpu” end
```

当然，duck typing 也适用于 Python 对象。不需要声明特殊类型

```
id_to_phoneme(processor) = processor.tokenizer.decoderphoneme_to_id(processor) = processor.tokenizer.encoder
```

您甚至可以在 Julia 代码调用 Python 时使用 Python 的“with”运算符(@pywith)

```
text_path = "text"
speech_path = "file"function get_logits(hf::HuggingFace,model,processor,item)
    speech, _ = hf.soundfile.read(item[speech_path])
    input_values = processor(speech, return_tensors="pt",
    sampling_rate=16000).input_values.to(device(hf)) 
    @pywith hf.torch.no_grad() begin
      logits=model(input_values["logits"].detach().to("cpu").numpy()[1,:,:]
      return logsoftmax(logits,dims=2)
    end
end
```

注意，返回值“logits”只是一个普通的 Julia 数组。PyCall 自动将 numpy 对象转换成标准的 Julia 对象。这里唯一重要的技巧是“[1，:]”。Python 使用基于 0 的索引，而 Julia 使用基于 1 的索引。

一旦我们在 Julia 中有了 logits，我们就可以使用标准的数学库。

```
return logsoftmax(logits,dims=2)
```

正如许多其他人所写的那样，数字处理是 Julia 真正闪光的地方。和 C 一样快，但是一个更有生产力的开发环境，有 REPL、调试器、垃圾收集器和强大的多重调度。不需要像 numpy 这样的库来加快速度，也不需要对代码进行矢量化。易于阅读的算法代码运行速度一样快。Julia 还对剖析、查找和修复瓶颈提供了强大的支持

这是一个对一组对数概率求和的示例函数。它使用 log-sum-exp 技巧来避免溢出(https://gregorygundersen . com/blog/2020/02/09/log-sum-exp/)。请注意我如何在第 2 行定义一个局部单行函数 e()，然后通过序列(xs)传播它(e .)。- m)，其中'- m '通过 xs 广播。这避免了 for 循环，并允许后端使用 fusion 进行潜在优化(https://Julia lang . org/blog/2017/01/moredots/)。是的，我可以写一个更紧凑的版本，但它不会运行得更快。可以在这段代码的任何一行设置断点，不涉及 C 库。

```
function logsumexp(xs)
    e(x) = if x==-Inf 0.0 else exp(x) end
    m=maximum(xs)
    if m==-Inf
        return -Inf
    else
        return m + log(sum(e.(xs .- m)))
    end
end
```

没有完美的环境，但我不得不说…这个组合真的很好！所以下次你想写一些快速代码，但是要用你喜欢的所有 Python 库，请考虑 Julia 调用 Python。

谢谢你

彼得（男子名）