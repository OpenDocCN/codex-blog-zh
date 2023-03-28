# 使现有的应用程序更加实用

> 原文：<https://medium.com/codex/making-an-existing-application-more-functional-90de07de7402?source=collection_archive---------12----------------------->

## **第 3 部分——异步行为及其整合**

![](img/e0672d22760731d749d3d28463c3331b.png)

由[约书亚·阿拉贡](https://unsplash.com/@goshua13?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

**简介**

在我们之前的[帖子](/codex/making-an-existing-application-more-functional-7835ac7bc605)中，我们展示了一个简单编写的 pet 应用程序的缺点，并概述了函数式编程(FP)思想如何帮助我们处理这些问题。接下来，我们用更合理的结构重写了应用程序，使用 monad 和 pure 函数来验证传入的数据。在本系列的最后一篇文章中，我们将转向与数据库的通信，并将所有的部分组合在一起。和以前一样，关于概念和 fp-ts 库的更多信息，请参见我写的那本书。

**异步调用**

让我们再次转向我们的 pets 文件夹的入口点， *entrypoint.ts* (参见 [Github repo](https://github.com/VanOvermeire/pet-project-new-version) ):

```
// **entrypoint.ts** (in pets)import * as TE from 'fp-ts/TaskEither';
// other importsexport const getPet = async (event: APIGatewayProxyEventV2): Promise<Response> =>
    pipe(
        preparePetGet(event),
        TE.***fromEither***,
        TE.***chain***(retrieveFromDatabase),
        TE.***map***(***JSON***.stringify),
        TE.***bimap***(handleError, okResponse),
        TE.***getOrElse***(T.***of***)
    )();

export const postPet = async (event: APIGatewayProxyEventV2): Promise<Response> =>
    pipe(
        preparePetPost(event),
        TE.***fromEither***,
        TE.***chain***(putInDatabase),
        TE.***bimap***(handleError, createdResponse),
        TE.***getOrElse***(T.***of***)
    )();
```

我们已经熟悉了“准备”步骤，在该步骤中，我们验证接收到的事件，并将其转换为域对象。我们还看到一些与数据库相关的功能( *retrieveFrom** ， *putIn** )。但是这个“TE”在我们的代码中做了什么呢？

从导入中可以看到，TE 来自 fp-ts 库，实际上被称为**task 要么**。它是一个 monad(更具体地说是一个 [monad transformer](https://en.wikipedia.org/wiki/Monad_transformer) )结合了处理异常(**)和异步行为(**任务**)的能力。鉴于很多 Javascript 是异步的*和*容易抛出错误，这确实是一个非常有用的单子！**

**这解释了为什么我们会看到*链*和*图*，因为它们是典型的单子函数。*from 要么*确实需要一个解释:它基于要么创建一个任务要么。因为我们把它放在了返回一个要么的 *prepare* 函数之后，它将获取这个单子并把它“提升”到 take 要么领域。这使得后续的异步调用更加容易:我们现在可以专注于映射和链接任务了。task 要么包装返回的要么也能“工作”。但它让事情变得复杂，却没有增加任何价值。**

**现在这一点更清楚了，让我们深入数据库逻辑。**

```
// **database.ts** (in pets)// imports and some comments

const buildGet = (request: RetrievePetRequest) => ({
    TableName: ***process***.env.DATABASE_NAME,
    Key: {
        ppId: `${***PETS_ID_PREFIX***}#${request.clientId}`,
        psId: request.id,
    },
});

const buildPost = (request: AddPetRequest) => ({
    TableName: ***process***.env.DATABASE_NAME,
    Item: {
        ppId: `${***PETS_ID_PREFIX***}#${request.clientId}`,
        psId: request.id,
        id: request.id,
        clientId: request.clientId,
        name: request.name,
        age: request.age,
        cuteness: request.cuteness,
        type: request.type
    }
});

export const retrieveFromDatabase = (request: RetrievePetRequest) =>
    pipe(
        buildGet(request),
        (params) => TE.tryCatch(() => get(params), serverError),
        TE.***chain***(checkEmptyTE),
        TE.***map***(getItem)
    );

export const putInDatabase = (request: AddPetRequest) =>
    pipe(
        buildPost(request),
        (params) => TE.tryCatch(() => post(params), serverError),
    );
```

**我们又一次有了组合和管道，因为所有导出的函数都是由更小的函数组成的。第一步是为我们的调用建立正确的参数，为此我们有一些简单的纯函数。下一步，我们将这些参数传递给 pass 要么构造函数 *tryCatch* 内部的实际 DynamoDB 调用。该函数的第二个参数是出错时的构造函数，在这种情况下，我们将返回服务器错误。在检索我们的项目之后，我们做一些额外的验证和映射:如果返回值为空，我们返回一个带有‘not found’错误的 *left* :**

```
// from **validation.ts** (in common)const checkEmpty = (item: GetItemOutput): E.Either<Errors, GetItemOutput> => item.Item ? E.***right***(item) : E.***left***(notFound());

export const checkEmptyTE = (res: GetItemOutput) => TE.***fromEither***(checkEmpty(res));
```

**如果我们确实找到了什么，我们就把它放到右边的*里*。正如我们之前看到的，有一些重复和共同的逻辑，对我来说，可读性的代价——也许还有复杂性——建议暂时保持原样。**

**现在回到入口点，对剩下的代码做一个简单的解释。最需要解释的是数据库调用后出现的 *bimap* 。当我们从数据库回来时，我们面临的“问题”是我们仍然在处理一个任务。它包含值或错误。根据实际存在的内容，我们将不得不做不同的事情(返回值或显示正确的错误代码)。有几种方法可以做到这一点，但是 bimap*非常好，可读性很强:如果有错误，将调用传入的第一个函数，如果一切顺利，将调用第二个函数。因此，第一个函数是 *handleError* (在之前的博文中讨论过)，而第二个函数是 *okResponse* 或 *createdResponse* 。这两个函数只是将我们的响应值包装成一个状态代码为 200 或 201 的 http 消息。***

**一旦完成，我们使用 *getOrElse* 来“解开”我们的任务，并获得底层任务。因为我们已经在上一步中处理了错误，所以在这里我们只需要告诉代码将任何错误包装在任务中，这样，无论我们采取哪条路径，我们都会得到相同的结果。由于任务只是一个返回承诺的函数，我们通过调用它来结束我们的函数。**

**还记得我之前说过在我们的路由中抛出一个错误是非常不理想的吗？现在我们知道如何解决这个问题了。不是在我们的入口点中“解包”任务，而是返回它。路由文件本身现在应该首先使用(Task)来检查路径是否有效，如果路径无效，则产生客户端错误。接下来，*链接*适当的入口点函数，使用 *getOrElse* 并进行函数调用。此外，如果有许多端点，我们可以通过使用字典来避免许多条件句的麻烦。[写完这一段后，我实际上为路由 lambda 事件创建了一个[小库](https://www.npmjs.com/package/fp-ts-lambda-routing)。它的主函数返回一个 task，让您决定何时真正调用潜在的 Promise。]**

**但是我们的野生动物呢？**

**没错。好了，验证和入口点代码几乎是相同的，所以我们可以把重点放在数据库上。**

```
// **database.ts** (in wild animals)// imports and commentsconst buildGet = (request: RetrieveWildAnimalRequest) => {
    return {
        TableName: ***process***.env.DATABASE_NAME,
        Key: {
            ppId: `${***WILD_ANIMALS_PREFIX***}#${request.type}`,
            psId: request.id,
        },
    };
};

const buildCounterGet = (request: RetrieveWildAnimalRequest) => {
    // similar, build some params
};

const buildPost = (request: AddWildAnimalRequest) => ({
    // similar, build some params
});

const buildCounterInit = (request: AddWildAnimalRequest) => ({
    // similar, build some params, with an exists check
});

const buildCounterUpdate = (request: AddWildAnimalRequest): UpdateItemInput => ({
    TableName: ***process***.env.DATABASE_NAME,
    Key: {
        ppId: `${***WILD_ANIMALS_PREFIX***}#${***COUNTER_PREFIX***}`,
        psId: request.type,
    },
    UpdateExpression: 'Set itemCount = itemCount + :incr',
    ExpressionAttributeValues: {
        ':incr': 1,
    },
    ReturnValues: 'UPDATED_NEW',
});

export const retrieveFromDatabase = (request: RetrieveWildAnimalRequest) => {
    return pipe(
        [buildGet(request), buildCounterGet(request)],
        requests => getParallel(requests),
        TE.***chain***(parallelCheckEmpty),
        TE.***map***(results => results.map(getItem))
    );
};

export const putInDatabase = (request: AddWildAnimalRequest) => {
    return pipe(
        buildCounterInit(request),
        (params) => TE.tryCatch(() => post(params), serverError),
        TE.***fold***(() => TE.***right***(request), () => TE.***right***(request)),
        TE.***chainFirst***((params) => TE.tryCatch(() => post(buildPost(params)), serverError)),
        TE.***chainFirst***((params) => TE.tryCatch(() => update(buildCounterUpdate(params)), serverError))
    );
};
```

**因为我们必须检索野生动物的计数器，所以需要更多的代码来创建数据库调用的参数。(这里又有一些我们可以修复的重复，以及传入环境变量——在我们以前的帖子中讨论过。)**

**让我们来看看野生动物和它的柜台。首先，我们构建检索动物的参数以及正确的计数器。因为这些调用之间没有依赖关系，所以我们并行执行它们。请注意，我们的 *getParallel* 方法 a)非常通用，因此被放在公共部分，b)基于现有代码构建(通用 *get* ):**

```
// from **gateway.ts** (in common)export const get = (params: GetItemInput) => client.get(params).promise();

export const getParallel = (inputs: GetItemInput[]): TE.TaskEither<Errors, GetItemOutput[]> => {
    return A.***array***.sequence(TE.***taskEither***)(
        inputs.map(input => TE.tryCatch(() => get(input), serverError))
    );
};
```

**因为我们现在获得了多个值，所以我们也有多个空检查要执行，我们再次使用 fp-ts 中的*序列*，并重用我们的代码来检查单个输出。最后，我们在 *TE.map* 中做一个数组 *map* 。**

**这篇文章有一点不同，因为我们正在重写的原始代码首先初始化计数器*，然后*发布动物，之后*更新计数器*。所以我们应该做一些类似的事情，但是用单子。如果你记住了原始应用程序的代码(你这个怪人)，你也可能会记得我们会捕捉计数器初始化时返回的任何错误，因为这样的错误可能只是表明计数器已经存在。有几种可能的方法。我们可以模仿原始代码，将我们的 *post* 包装在任务中，或者是在一个尝试/捕捉中。但是这将导致丑陋和误导性的代码:我们正在处理一个任务，但是事实上没有任何错误被抛出！**

**一个可行的替代方案是首先使用一个任务，捕捉所有错误，然后将该任务提升为一个任务，就像我们之前对这两个任务所做的那样。这样，我们的类型就更准确了。这里，我们选择了第三种方法:将 init counter 调用保留在 task 要么(这是诚实的，因为调用可能会抛出错误)*中，但*忽略 *putInDatabase* 中的失败，通过使用 *fold* 总是返回原始请求。这使得捕捉错误成为我们流程中更显而易见的一部分，代价是一个可能愚蠢的*折叠*步骤。接下来的两个步骤使用 *chainFirst* 进行调用，以保存动物并更新计数器，这类似于*链*，除了它将传递原始传入参数，即我们的请求。我们这样做是因为我们的更新计数器不需要 *post* 值，但是需要原始请求来创建一个键。**

****优点****

**自从上一篇博文以来，这一直是我们长期以来的传统，让我们思考一下这种方法的优势。**

*   **如前所述，浏览代码并找到相关的代码片段非常容易。野生动物计数器必须以不同的方式工作？没问题，查查野生动物文件夹。我们要给宠物再加一个属性？去那个文件夹！带有实用功能的公共文件夹有望保持足够的通用性，以至于您很少需要探究实际的实现。 *get* 检索一个项目， *getParallel* 并行检索项目*s*——并行。名称和类型中包含了您需要的所有信息。**
*   **在上一篇文章中也提到，我们试图通过指定只有入口点方法可以被外人使用来限制代码的表面积。**
*   **一旦我们超越了入口点，现在“挂钩”函数就更容易了。想找回一只宠物？只需传入任何包含 id 和客户端 id 的对象！在一个具有更有意义的领域的更大的应用程序中，大多数函数将具有更简单的，或者至少是合理的类型。在这里，由于我们有限的领域逻辑，大多数都与接收、验证和转换传入事件有关。**
*   **您可能已经注意到，对数据库的基本调用都在 common 文件夹中，在一个名为 *gateway* 的文件中。这个名字不是我自己取的。我偷了它，因为它似乎是一个很好的方式来表明与“外部世界”沟通的重点。除了重用之外，将这些调用与其他文件夹中的域逻辑分开也提高了可测试性，除了那些基本的调用，它们只不过是 AWS SDK 的包装器(AWS 本身可能已经测试得很好了)。通过将对外部世界的实际调用隔离在一个单独的文件中，更改我们的数据库也应该变得更容易。请注意，如果这是我们的意图，我们应该创建自己的参数，而不是用 AWS 的 *GetItemInput* 之类的东西来破坏我们的数据库文件。**
*   **测试很容易，因为(除了环境变量的注入之外)我们有许多纯函数，它们只基于传入的参数产生输出。这使得覆盖代码中所有可能的路径变得更加容易，就像有限数量的条件分支一样。所以只需很少的努力，我们就能获得很高的覆盖率。**
*   **这些纯粹的小功能使复杂性远离。更高层次的函数是使用组合创建的，主要使用*管道*和单子。在实际应用中，我们会有更多没有单子的函数组合(使用 fp-ts *flow* )和更多的高阶函数，但是原理是一样的。**
*   **这种对小而集中的函数组合的关注与 *dynamoDb.js* 中做得太多的函数形成了鲜明的对比。作为前者的一个例子，参见 *getItem* ，这是一个非常简单的转换，每当我们从数据库中检索一个项目时，它都是可重用的。这有助于避免复杂性，尽管我们在组织代码库时应该格外小心，仔细考虑命名、文件夹结构、导出等问题。**
*   **多亏了管道、合成和单子，我们为更复杂的函数提供了一个很好的线性“流程”,每个函数返回一个值，然后传递给下一个函数。不会因为异常或错误而突然停止。**
*   **我们的类型检查很简单，有很好的类型推断，但仍然提供了使我们的应用程序不容易出错的价值。类型系统本身通常足以知道代码是否会工作。[我想补充的是，IntelliJ 中的 Typescript 可能很挑剔，接受一些有效的代码，然后在重新启动我的 IDE 时抛出错误。令人讨厌，但不是一个交易破坏者。]**
*   ***types.ts* 中一个容易被忽略的细节:每个属性都被设置为 *readonly* ，这意味着 Typescript 将试图确保没有对象发生变异。由于 Typescript 没有抛出错误，我们似乎已经避免了变异！大获成功。**
*   **最初的应用程序擅长的性能从来不是我们主要关心的问题。尽管如此，webpack 的树摇动意味着我们得到一个小的 Javascript 文件，而不是相对较大的原始应用程序 zip (4 KB 对解压缩后的 170+)。虽然我没有做任何适当的比较，但我所拥有的几个数据点表明平均运行时间稍微好一点，最小值只有 5 毫秒。由于我们现在使用了许多纯函数， [memoization](https://en.wikipedia.org/wiki/Memoization) 是一种在需要时进一步加速的简单方法，对代码复杂性几乎没有影响！**

****总结****

**在这篇博文中，我们看了一下我们新的、改进的、功能性的应用程序，以及验证和结构之外的变化，重点关注异步行为以及代码如何组合在一起。我们看到了如何使用 Task 和 Task monads 作为工具来保持我们的代码完全按照以前的方式运行，即一个接一个地执行任务的线性流程。并行运行任务也非常容易。我们最后概述了我们的函数方法的一些优点，尽管其中大部分现在可能已经很熟悉了。**

**在结束之前，我想指出 FP 不会神奇地解决你所有的代码问题。但是除了我们在这些帖子中讨论的其他事情，我真的很喜欢阅读这样的代码是多么容易，一旦你习惯了:**

```
// **entrypoint.ts** (in pets)// other codeexport const getPet = async (event: APIGatewayProxyEventV2): Promise<Response> =>
    pipe(
        preparePetGet(event),
        TE.***fromEither***,
        TE.***chain***(retrieveFromDatabase),
        TE.***map***(***JSON***.stringify),
        TE.***bimap***(handleError, okResponse),
        TE.***getOrElse***(T.***of***)
    )(); 
```

**当你开始在精神上剥离所有的胶水时更是如此:**

```
**getPet =>** preparePetGet,
retrieveFromDatabase,
***JSON***.stringify,
handleError || okResponse
```

**希望你喜欢这些帖子！**

**萨姆·范·奥弗迈尔**