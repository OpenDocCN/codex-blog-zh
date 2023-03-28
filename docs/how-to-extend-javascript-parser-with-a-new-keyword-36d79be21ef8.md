# 如何用新关键字扩展 JavaScript 解析器

> 原文：<https://medium.com/codex/how-to-extend-javascript-parser-with-a-new-keyword-36d79be21ef8?source=collection_archive---------26----------------------->

> “难道你看不出新话的全部目的是要缩小思想的范围吗？最终，我们将使思想犯罪实际上不可能发生，因为将没有词语来表达它。”——《1984》，乔治·奥威尔

让我们假设你想要在 **JavaScript** 中使用 **fn** 关键字而不是**函数**，类似于 Rust 中的工作方式。如何让这成为可能？

好吧让我们从 [**橡子**](https://github.com/acornjs/acorn) 开始。

> 一个小巧快速的 JavaScript 解析器，完全用 JavaScript 编写。

你每天使用的工具，如 ESLint 或 Babel，都是基于 acorn 的。这对你来说又快又简单。

它可以这样使用:

```
const {Parser} = require(["acorn"](https://github.com/acornjs/acorn))

const MyParser = Parser.extend(
  require(["acorn-jsx"](https://github.com/acornjs/acorn-jsx))(),
  require(["acorn-bigint"](https://github.com/acornjs/acorn-bigint))
)
console.log(MyParser.parse("// Some bigint + JSX code"))
```

甚至更多！它可以扩展为:

```
module.exports = function noisyReadToken(Parser) {
  return class extends Parser {
    readToken(code) {
      console.log("Reading a token!")
      super.readToken(code)
    }
  }
}
```

如果你想使用这样的代码:

```
fn hello() {
    return 'world';
}
```

并将其解析为

```
function hello() {
    return 'world';
}
```

警惕不要！比你想的简单多了。所有需要做的就是:**扩展 acorn 解析器**。

# 如何扩展 acorn 解析器？

在我们开始之前，有几件事你需要知道。

我们在 JavaScript 中使用的所有保留字，如:if、else、function 等，都位于**parser . acorn . keyword types**对象中，所以如果你想添加一个新的关键字，你需要创建一个新的令牌:

```
const {Parser, TokenType} = require(['acorn'](https://github.com/acornjs/acorn));

function newSpeak(Parser) {
    Parser.acorn.keywordTypes['fn'] = new TokenType('fn', {
        keyword: 'fn',
    });
}const {parse} = Parser.extend(
    newSpeak,
);

console.log(parse(`
    fn hello() {
        return 'world';
    }
`));
// here you will see the AST
```

T21 是我们虚构语言的名称。

下一步应该是将 **fn** 关键字添加到 acorn 知道的关键字列表中:

```
function newSpeak(Parser) {
    Parser.acorn.keywordTypes['fn'] = new TokenType('fn', {
        keyword: 'fn',
    });

    return class extends Parser {
        parse() {
            this.keywords = addKeyword('fn', this.keywords);
            return super.parse();
        }
    }}function addKeyword(keyword, keywords) {
    const str = keywords
        .toString()
        .replace(')$', `|${keyword})$`)
        .slice(1, -1);

    return RegExp(str);
}
```

**关键词**是中的一个大正则表达式，下面是它的样子:

```
/^(?:break|case|catch|continue|debugger|default|do|else|finally|for|function|if|return|switch|throw|try|var|while|with|null|true|false|instanceof|typeof|void|delete|new|in|this|const|class|extends|export|import|super)$/
```

所以，是的，我们需要把我们的关键字`fn`添加到这个列表中，以使事情运转起来。

之后我们需要覆盖`parseStatement`，并告诉 acorn 这只是`fn` 只是`function`。

```
function newSpeak(Parser) {
    Parser.acorn.keywordTypes['fn'] = new TokenType('fn', {
        keyword: 'fn',
    });

    return class extends Parser {
        parse() {
            this.keywords = addKeyword('fn', this.keywords);
            return super.parse();
        }
        parseStatement(context, topLevel, exports) {
            if (this.type == Parser.acorn.keywordTypes['fn']) {
                this.type = Parser.acorn.keywordTypes['function'];
            } return super.parseStatement(context, topLevel, exports);
        }
    }
}
```

就是这样！以下是所有代码:

```
const {TokenType, Parser} = require(['acorn'](https://github.com/acornjs/acorn));

function newSpeak(Parser) {
    Parser.acorn.keywordTypes['fn'] = new TokenType('fn', {
        keyword: 'fn',
    });

    return class extends Parser {
        parse() {
            this.keywords = addKeyword('fn', this.keywords);
            return super.parse();
        }
        parseStatement(context, topLevel, exports) {
            if (this.type == Parser.acorn.keywordTypes['fn']) {
                this.type = Parser.acorn.keywordTypes['function'];
            }
            return super.parseStatement(context, topLevel, exports);
        }
    }
}

function addKeyword(keyword, keywords) {
    const str = keywords
        .toString()
        .replace(')$', `|${keyword})$`)
        .slice(1, -1);

    return RegExp(str);
}

const MyParser = Parser.extend(
    newSpeak,
);

console.log(MyParser.parse(`
    fn hello() {
        return 'world';
    }
`));
```

现在你可以添加任何你想要的关键字来创造你梦想的语言:)！