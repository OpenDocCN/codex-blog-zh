# 如何用 Spring 数据 JPA 轻松做条件查询

> 原文：<https://medium.com/codex/how-to-easily-do-conditional-querying-with-spring-data-jpa-123079aa09a6?source=collection_archive---------0----------------------->

## "在与数据库对话时，利用领域驱动设计中的规范模式."

![](img/ad2b09c99a5a81732ff6a5c0b61771dc.png)

很难找到一个现代应用程序不使用多种数据持久性形式中的一种。无论是对数据流进行繁重而复杂的操作/分析的应用程序，还是保存用户记录及其交互方式的简单在线应用程序，当应用程序停止占用易失性内存形式的任何空间，但仍需要跟踪一些数据以供将来参考时，几乎总是有一个地方可供应用程序保存数据。

如果你碰巧在读这篇文章，你可能已经在使用 Spring，并且很可能知道 Spring 框架背后的团队如何投入巨资创建库，以使编码更容易、更快、更干净，并且在我个人看来，更有趣。

考虑到数据持久性角色在现代开发中的重要性和生命力，您甚至不用检查就可以猜到 ***Spring*** 已经提供了多种方式和方法来完成这项任务。

Spring Data JPA、JDBC、MongoDB、R2DBC、Redis 只是其中一大堆流行的库。

在这里，我们研究 JPA 以及如何实现更高级的(动态的和有条件的)查询场景，当产品的业务逻辑或 UI 变得更复杂时，这是必要的。

我将假设您熟悉基本的数据访问概念，特别是 Spring 数据，比如存储库、实体和……但是，如果您需要熟悉这些概念，我会尽量至少提到一些必要的主题。

好了，介绍够了；让我们看看它是如何工作的！

想象一下，为一家包裹保险公司开发一个假设的应用程序，为包裹从源头到目的地的整个旅程提供保险！下面是保险实体的简化版本:

```
@Entity
*public class* Insurance { @Id
    @GeneratedValue
    *private* id Long; *private* String insuranceNumber;

    @OneToOne(optional = *false*, cascade = CascadeType.*ALL*)
    *private* Client client;

    @OneToOne(optional = *false*, cascade = CascadeType.*ALL*)
    *private* Parcel parcel;

    @Enumerated(EnumType.*STRING*)
    *private* Status insuranceStatus;

    @ManyToOne
    *private* Trip trip; @ManyToOne
    *private Post*Company postCompany; @ElementCollection
    *private List*<Damage> damages = *new* ArrayList<>();

    @OneToMany(mappedBy = "insurance", cascade = CascadeType.*ALL*)
    *private List*<Payment> payments = *new* ArrayList<>(); *protected* Insurance() {}
    // accessors and mutators ...}
```

正如您所看到的，在面向对象的层次结构中，我们有一些与我们的保险实体相关的其他实体，因此在我们的数据库中有一些其他的表来表示它们。

在这样的应用程序中，显然需要搜索一堆具有特定细节的保险记录。

让我们从简单的搜索查询开始，直到更复杂和更有条件的查询。

1.  ***第一个场景:***

我们想要搜索所有仍然有效的保险的列表。(未归档)所以我们需要做的是基于属性 *insuranceStatus* 的值进行查询，尽管它没有设置为 archived。

这里，我们的保险存储库看起来像这样:

```
*public interface InsuranceRepository
    extends CrudRepository*<Insurance, Long>
{

    // Some generic CRUD methods ... List<Insurance> findAllByStatusNot(Insurance.Status status);
}
```

我们上面所做的是使用一种叫做 ***派生查询方法、*** 的技术，其中你指示 Spring JPA 根据你写的方法名为你构建一个查询。在这个例子中，我们只是告诉 JPA 找到所有保险实例，它们的 ***状态*** 属性值是*而不是我们作为参数传递给方法的*。因此，它将返回一个包含所有未设置为存档的保险记录的列表。很简单，不是吗？

对于构建这种类型的方法，有相当多的已定义的关键字可以用来模拟数据库查询，然后 JPA 将在幕后为您创建这些查询。点击查看完整列表[。](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.query-methods)

由于您在上面的结果中得到的是一个列表，并且取决于实际情况，该列表可以包含从几个到一千多个的内容；在大多数情况下，最好在后端处理分页和排序，而不是获取所有内容，然后在前端处理这些任务，原因很明显。

在 Spring JPA 中，通过从 *JpaRepository* 而不是 *CrudRepository* 扩展您的存储库，然后将可分页或排序对象传递给您编写的查询方法，您可以很容易地做到这一点。( *JpaRepository* 扩展了另外两个接口，其中一个是 *PagingAndSortingRepository。我们将在本文后面讨论另一个问题。)*

下面是我们现在使用分页的例子:

```
*public interface InsuranceRepository
    extends JpaRepository*<Insurance, Long>
{

    // Some generic CRUD methods ... *Page*<Insurance> findAllByStatusNot(Insurance.Status status,               .                                      *Pageable* pageable);
}
```

> 你要知道，当使用 Spring JPA 时，总是可以通过 *@Query* 注释使用原生查询或 JPQL 查询直接在存储库中编写查询。在本文中，我不打算解释如何利用@Query 注释来编写本机或 JPQL 查询，但值得注意的是，使用它会失去类型安全性，并且在更复杂和条件查询的情况下，您将最终连接多个字符串来创建最终的查询，这很容易出错，并且在将来发生任何变化的情况下很难维护。

![](img/497780ee2dc3a36f56ed694eca3e6520.png)

2. ***第二个场景:***

现在，让我们扩展前面的查询，搜索包含更多条件的保险(我们的实体)列表。假设我们在数据库中寻找保险记录，除了**而不是**将它们的状态设置为*存档之外，在一些列中有*特定的关键字(Amsterdam ),并按保险号排序。

例如，包裹的来源或目的地有这个词，或者可能是客户或公司的名称，基本上是任何可能有这条信息的记录。

像在第一个场景中一样，让我们编写一个 ***派生的查询方法*** 来处理这种情况；为什么不呢？这可以很快完成，并且不需要了解太多 SQL 的语法，所以我们开始吧:

```
*public interface InsuranceRepository
    extends JpaRepository*<Insurance, Long>
{

    // Some generic CRUD methods ...Page<Insurance> findAllByStatusNotAndParcelDescriptionContainingOrClientNameContainingOrTripSourceContainingOrTripDestinationContainingorPostCompnayNameContainingOrderByInsuranceNumberDesc(Insurance.Status status, String parcelDescription,String clientName, String tripSource, String tripDestination, String postCompanyName, Pageable pageable);
}
```

我认为可以肯定地说，在我们大多数人的眼中，这是 ***丑陋的******不可读的*** ，以及难以维护的*，因为如果你改变了你想要的查询构建方式，你需要修改方法签名，并将其应用到正在使用的代码中的所有其他地方。*

*因此，很明显，这不应该是正确的做法，正如我们已经讨论过的，编写 JPQL 或 SQL 并不能很好地解决条件查询的问题。*

***通过实例查询 API 来救援！(或者不是！)***

*正如我前面提到的，[***jparestory***](https://docs.spring.io/spring-data/jpa/docs/current/api/org/springframework/data/jpa/repository/JpaRepository.html)*接口扩展了另外两个接口，其中一个是[***paginandsortinstory***](https://docs.spring.io/spring-data/commons/docs/current/api/org/springframework/data/repository/PagingAndSortingRepository.html)***<T，ID >*** *(* 我们试了一下*，*但是**

```
**public interface JpaRepository<T, ID> extends PagingAndSortingRepository<T, ID>, QueryByExampleExecutor<T> {}**
```

**这个接口为我们提供了各种不同但熟悉的***find****方法，这些方法也接受一个[实例的实例](https://docs.spring.io/spring-data/commons/docs/current/api/org/springframework/data/domain/Example.html)。***

```
***public interface **QueryByExampleExecutor**<**T**> {
     <S extends T> Optional<S> **findOne**(Example<S> var1);     
     <S extends T> Iterable<S> **findAll**(Example<S> var1);     
     <S extends T> Iterable<S> **findAll**(Example<S> var1, Sort var2);
     <S extends T> Page<S> **findAll**(Example<S> var1, Pageable var2);
     <S extends T> long **count**(Example<S> var1);     
     <S extends T> boolean **exists**(Example<S> var1); 
}***
```

***它是如何工作的？最简单地说: [*示例*](https://docs.spring.io/spring-data/commons/docs/current/api/org/springframework/data/domain/Example.html) 实际上是您想要搜索的实体的一个示例，它以这样的方式构建:“**查找与我相似的实体记录！**”***

**当使用 [*查询示例 API*](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#query-by-example) 时，你可以用比以前更清晰的代码做很多事情。API 中融入了三个概念: ***探针*** 、 ***范例匹配器、*** 和 ***范例。*****

****第一个**是您正在查询的域对象的实际例子，其中填充了所需的字段。**第二个**是如何比较和匹配与探针相关的数据库记录的指示，最后一个**是上面两个的组合，它被传递给 *find* 方法之一并为我们创建查询。****

**只有在没有嵌套对象的情况下，这才是一个好方法(就像我们上面所做的那样)，除了简单之外，它还带来了几个主要的限制，在决定使用这个 API 之前，必须考虑这些限制。**

*   **不支持嵌套或分组属性约束，如`firstname = ?0 or (firstname = ?1 and lastname = ?2)`。**
*   **仅支持字符串的 starts/contains/ends/regex 匹配和其他属性类型的精确匹配。**

**出于第一个原因，我们不能利用它来重现我们上面已经做过的查询。**

**所以对于简单的对象(类似下面这个人)，可以写类似这样的东西:**

```
****public** **class** **Person** {     **@Id**   **private** String id;
    **private** String firstname;
    **private** String lastname;
    **private** Address address;
    *// … getters and setters omitted
* }Person person = **new** Person();                                person.setFirstname("Dave");                             ExampleMatcher matcher = ExampleMatcher.matching()         .withIgnorePaths("lastname")                            .withIncludeNullValues()                                .withStringMatcherEnding();                            Example<Person> example = Example.of(person, matcher);Optional<Person> actual = repository.findOne(example);**
```

**我们在上面所做的是创建一个我们正在寻找的模型的实例，将它的 firstName 属性设置为我们正在寻找的关键字，并使用自定义的 ExampleMatcher 来包装整个示例，它在搜索相似记录时基本上应用了所需的行为。**

**因此，显然 ***查询示例 API*** 在更复杂的查询中不会帮助我们，尽管它提供了动态查询创建，而不需要编写任何 SQL 本身，这可以根据情况派上用场。**

****用“规范”来做吧！****

**那么，在我们的例子中，对于派生查询方法*或者将 SLQ 字符串相互连接起来，真正的替代方法是什么呢？***

***实际上，我想解释两种可能的情况，一种是**标准 API 及其规范**，另一种是 **QueryDSL。前者将是本文的最后一部分，而后者我将在另一篇文章中单独描述。*****

***规范接口源自 Eric Evans 的领域驱动设计书中介绍的概念。它将规范定义为实体上的谓词，这正是规范接口所代表的。***

***为了使用它，您必须扩展您的存储库接口，从*JpaSpecificationExecutor*<T>:***

```
***public interface InsuranceRepository
    extends JpaRepository*<Insurance, UUID> ,    *JpaSpecificationExecutor*<Insurance> {}**
```

**这里的想法是，我们为我们想要在目标实体的记录中检查/搜索的每个条件创建动态规范，然后根据我们的需要用适当的逻辑将它们链接在一起。**

**将由单个规范链接而成的对象传递给 *JpaSpecificationExecutor* 接口的默认方法将会为我们完成这个任务。**

```
***public interface JpaSpecificationExecutor*<T> {
Optional<T> findOne(@Nullable *Specification*<T> spec);
 *List*<T> findAll(@Nullable *Specification*<T> spec);
 *Page*<T> findAll(@Nullable *Specification*<T> spec, *Pageable* pageable);
 *List*<T> findAll(@Nullable *Specification*<T> spec, Sort sort);
 *long* count(@Nullable *Specification*<T> spec);
}**
```

**让我们看看如何…我们首先为每个条件创建方法，从我们的目标模型的类中返回一个规范对象。**

```
***static Specification*<Insurance> withTripSource(@Nullable String source) {*return* (root, cq, cb) -> title == *null* ? *null* : cb.like(cb.lower(root.join("trip", JoinType.*LEFT*).get("source")), cb.lower(cb.literal("%" + source + "%")));}**
```

**乍一看，这似乎很复杂，但实际上比你想象的要简单。**

**这里，我们将方法名设置为“ *withTripSource* ”，以表明我们希望将 trip.source 内容作为查询的主题/条件之一。**

**下一部分非常有用，因为它给了我们自由，如果我们没有提供任何要查找的源字符串，我们可以在最终的规范对象中动态地忽略这个方法的结果。**

**这个行为是我们拥有的关键功能，它允许我们在即将到来的请求中接受动态参数，并基于此创建一个动态查询(后台)。比方说，我们可能总是不想根据 trip.source 过滤我们的实体，将 null 传递给这个方法将会照顾到我们的意图。**

```
**Specification<Insurance> specs = Specification
        .where(withStatus(status))
        .and(withTripSource(source))
        .and(withTripDestination(destination))
        .and(withPostCompanyName(name));

Pageable paging = PageRequest.of(page, size);
*return* insuranceRepository.findAll(specs, paging);**
```

**使用 Criteria API 将为您提供很大的灵活性，让您可以根据自己的需要创建运行时动态查询(equals，contains …)。**

**在从你想要拥有的所有规格中构建链条时，除了 ***之外，其中*** & ***和*** *、* ***非*** 和 ***或*** 也是可用的。**

**您可以(或者我认为您必须)做的另一件事是通过使用元数据为上面的构造添加类型安全。**

**提供类型安全的另一种方法是 QueryDSL，已经提到过了，将会有另一篇文章来描述它。**

**祝你好运，享受编码！**

**参考文献:**

**[https://docs . spring . io/spring-data/JPA/docs/current/reference/html/# JPA . repositories](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.repositories)**