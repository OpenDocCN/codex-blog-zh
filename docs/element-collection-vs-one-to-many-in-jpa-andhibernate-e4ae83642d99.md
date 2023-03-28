# JPA 和 Hibernate 中的元素集合与一对多

> 原文：<https://medium.com/codex/element-collection-vs-one-to-many-in-jpa-andhibernate-e4ae83642d99?source=collection_archive---------4----------------------->

![](img/aa0dd8e0bfafdb8dd1e9d7e10281d8f4.png)

布莱克·康纳利在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

![](img/5edc2aaa3ec4df622dcf5e56326ca1c4.png)

## 简介:

任何项目内部都会有一个数据库，用于保存和从中获取数据，并将数据保存在表中。您可以使用关系数据库，如 MySQL 和 Postgress，也可以使用 NoSQL 数据库，如 Mongo DB 或 Cassandra DB。

顺便说一下，本文将在 spring 项目中解释 JPA，用于表示关系数据库，我将重点关注**一对多**注释和**元素集合**注释。

## 春靴中的 JPA:

Spring Boot JPA 是用于管理 Java 应用程序中关系数据的 Java 规范。它允许我们在 Java 对象/类和关系数据库之间访问和持久化数据。JPA 遵循对象关系映射(ORM)。它是一组接口。

> 有关 [JPA](https://www.javatpoint.com/spring-boot-jpa#:~:text=Spring%20Boot%20JPA%20is%20a,is%20a%20set%20of%20interfaces.) 的更多信息

## 一对多注释:

如果我们创建两个实体(表格)之间的关系，我们需要使用**一对多注释**。

请看下面的代码:

我们可以表示商店，商店有许多分支，它们都是一个实体，这意味着它们在我们的数据库中都有一个表。

`Strore.java`

```
@Entity
@Table(name = "store")
public class Store{@Id    
@GeneratedValue(strategy = GenerationType.IDENTITY)    
private Long id; @Column(name = "name")    private String name; @Column(name = "ad_video_url")    private String adVideoUrl;@OneToMany(mappedBy = "store" , cascade = CascadeType.ALL)       
private Set<Branch> branches = new HashSet<>();}
```

而`branch.java`

```
@Entity
public class Branch{
@Id    
@GeneratedValue(strategy = GenerationType.IDENTITY)    
private Long id;@Column(name = "name")    
private String name;@Column(name = "ad_video_url")    
private String adVideoUrl;//if you want you can remove the following annotation
@ManyToOne    
@JsonIgnoreProperties(value = "branches", allowSetters = true)    private AscStore store;} 
```

运行代码后，hibernate 将在数据库中创建两个表，第一个表称为 store，第二个表称为 branch。现在我们需要为两个实体创建一个存储库，以便从数据库中插入和获取数据。

我们可以使用`@Repository`来创建一个存储库组件。

`StoreRepository.java`

```
@Repository
public interface StoreRepository extend JpaRepository<Store,Long>
{
}
```

现在我们需要为分支实体创建一个存储库。

`BranchRepository.java`

```
@Repository
public interface BranchRepository extend JpaRepository<Branch,Long>
{
}
```

在那之后，我们在**店**和**分店**之间有了新的关系

## Elementcollection 批注:

ElementCollection 是一个标准的 JPA 注释，现在它比专有的 Hibernate annotation collection ofelements 更受欢迎。

意味着集合不是实体的集合，而是简单类型(字符串等)的集合。)或可嵌入元素的集合(用`@embeddable`标注的类

这也意味着元素完全由包含实体所拥有:当实体被修改时它们被修改，当实体被删除时它们被删除，等等。他们不能有自己的生命周期。

看看下面的代码，看看我们如何实现 **Elementcollection** 类。

最简单的术语， **@ElementCollection** 告诉编译器我们正在映射一个集合，其中， **@CollectionTable** 给出目标表的名称，然后 **@JoinColumn** 指定我们要连接的实际列，如下所示:

从上一个例子中，我们现在有了两个实体**商店**和**分支**，以及它们之间的关系，现在我将添加一个新的类**产品**。

`product.java`阶级

```
@Embeddable
@Data
public class product{// we dont need to use id becouse it is not a entity@Colume(name="product_name")
private String productName;@Colume(name="product_prict")
private Double productPrice;}
```

现在我们将在`store.java`类中添加一个新的 **Elementcollection** 注释，并从**产品**类中添加一个新的**产品**对象。

```
@Entity
@Table(name = "store")
public class Store{@Id    
@GeneratedValue(strategy = GenerationType.IDENTITY)    
private Long id;@Column(name = "name")    private String name;@Column(name = "ad_video_url")    private String adVideoUrl;@OneToMany(mappedBy = "store" , cascade = CascadeType.ALL)       
private Set<Branch> branches = new HashSet<>();@ElementCollection(fetch = FetchType.LAZY)
@CollectionTable(name = "store_product", joinColumns = @JoinColumn(name = "store_id", nullable = false), uniqueConstraints = @UniqueConstraint(columnNames = {"store_id"}))
private Set<Product> products = new HashSet<>();}
```

现在创建之后，您的数据库将看到 Product 表以`storeId`作为主键。但它是商店表的外键，而不是产品表的主键。

可嵌入对象没有**存储库**，因为它依赖于实体，例如**产品可嵌入对象**依赖于**商店实体**。我们可以在不使用**存储库的情况下插入它。**

## 这两种方法的区别在于:

**实体:**

当创建一个实体时，您可以将它与 JPA 存储库相关联，以编写您的查询或使用 JPA 的内置查询。

**可嵌入:**

当创建一个可嵌入的类时，必须与任何实体类相关，因为它依赖于实体类。我们不能创建一个仓库。

## 结论:

每个应用程序都有数据，这些数据将在程序中显示给应用程序客户端，因此我们有一个数据库、Jpa 和 hibernate。

但是我们如何设计数据库，取决于应用程序的业务逻辑。

不要忘记在文章上拍手，每篇文章可以拍手 50 次。还有你在这里找到我的[**LinkedIn**](https://www.linkedin.com/in/abd-alrhman-alkraien-83a93a1b1/)

## 参考资料:

*   [https://www . Java point . com/Spring-boot-JPA #:~:text = Spring % 20 boot % 20 JPA % 20 is % 20a，is % 20a % 20 set % 20 of % 20 interfaces](https://www.javatpoint.com/spring-boot-jpa#:~:text=Spring%20Boot%20JPA%20is%20a,is%20a%20set%20of%20interfaces)。
*   [https://stack overflow . com/questions/8969059/difference-between-one tomany-and-element collection #:~:text = I % 20 believe % 20% 40 element collection % 20 is % 20 majority，what % 20 you % 20 want % 20 to % 20 achieve](https://stackoverflow.com/questions/8969059/difference-between-onetomany-and-elementcollection#:~:text=I%20believe%20%40ElementCollection%20is%20mainly,what%20you%20want%20to%20achieve)。
*   [https://medium . com/nerd-for-tech/element collection-vs-onetomany-in-hibernate-7 FB 7d 2 AC 00 ea](/nerd-for-tech/elementcollection-vs-onetomany-in-hibernate-7fb7d2ac00ea)