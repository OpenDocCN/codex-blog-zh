# DDD 的服务人员最后解释道

> 原文：<https://medium.com/codex/services-in-ddd-finally-explained-230b5f8a199c?source=collection_archive---------1----------------------->

![](img/0194aeb61914bce84c0e6580ec232aec.png)

我注意到，理解领域驱动开发环境中的服务以及应用程序、领域和基础设施层中的服务之间的区别始终是一个挑战。

领域驱动设计在 IT 环境中做了大量清理，征服了程序员的心。Eric Evans 是推广这种并不新颖的软件开发方式的最著名的人之一。不幸的是，有时命名它的元素可能会导致完全理解 DDD 的某些部分背后的思想的一些问题。其中之一是服务。

我将用一个例子来解释这个主题——一个库。在图书馆，你有可以借书的读者。这本书可以有也可以没有。它有标题，作者等等。我们有领域对象，如图书馆、读者、书籍和图书管理员。

下面你可以找到一些核心域对象的简单例子。第一个是一本书。

```
package ddd.explained.library.domain;

public class Book {
   private int bookId;
   private boolean borrowed;
   private boolean readingRoomOnly;

   Book(int bookId, boolean borrowed, boolean readingRoomOnly) {
       this.bookId = bookId;
       this.borrowed = borrowed;
       this.readingRoomOnly = readingRoomOnly;
   }

   boolean canBeBorrowed() {
       return !borrowed && !readingRoomOnly;
   }
}
```

第二个实现了 Reader 对象。

```
package ddd.explained.library.domain;

public class Reader {
   private int readerId;

   Reader(int readerId)
   {
       this.readerId = readerId;
   }

   public boolean lend(Book book) {
       if (book.canBeBorrowed()) {
           return false;
       }

       // some impl

       return true;
   }
}
```

我决定不实现整个域逻辑，因为这会降低可读性。域对象不应该关心底层细节。甚至不知道他们的存在。

## **域名服务**

域层中的服务负责这方面的操作。在我们的例子中，服务将负责回答读者是否可以借书的问题。图书的副本可能会用完，或者读者可能已经拥有最大数量的图书，或者其帐户已被禁用。这本书只能在阅览室里买到。正如您所看到的，该服务拥有许多域对象并对它们进行编排。在我们的例子中，LibraryService 服务将调用域对象上的操作。

```
package ddd.explained.library.service;

public class LibraryService {
   public boolean lend(Reader reader, Book book)
   {
       if (reader.canLend(book)) {
          reader.lend(book);

           return true;
       }

       return false;
   }
}
```

## **应用服务**

在大多数情况下，我们不仅仅想要保存更改。我们希望与用户或其他服务进行一些交互。这是应用服务进入舞台的地方。应用层中的服务负责从域外获取输入数据，返回关于动作结果的信息，监听回答并决定是否应该发送通信消息。

你可能会注意到应用服务的责任是与世界联系。但是，您应该记住，在这种情况下，信息从哪里来或者到底是什么发送消息并不重要。它的发生很重要。

```
package ddd.explained.library.application.service;

import ddd.explained.application.service.NotificationService;

import ddd.explained.domain.ReaderRepository;
import ddd.explained.domain.service.BookRepository;

public class LibraryService {
   private ddd.explained.domain.service.LibraryService libraryService;
   private NotificationService notifications;

   private ReaderRepository readerRepository;

   private BookRepository bookRepository;

   public LibraryService(ddd.explained.domain.service.LibraryService libraryService, NotificationService notifications, ReaderRepository readerRepository, BookRepository bookRepository) {
       this.libraryService = libraryService;
       this.notifications = notifications;

       this.readerRepository = readerRepository;

       this.bookRepository = bookRepository;

   }

   public boolean lend(int readerId, int bookId)
   {

       Reader reader = this.readerRepository->find(readerId);

       Book book = this.bookRepository->find(bookId);

       if (libraryService.borrow(reader, book)) {
           bookRreaderRepositoryepository->update(book);

           bookRepository->update(reader);
           notifications.sendEmailAboutLoan(book, reader);

           return true;
       }

       return false;
   }
}
```

理想的应用程序服务应该被带到您的应用程序中，以正确的方式调用，所有的魔法都应该完成。这本书将被预订(如果没有或其他要求没有得到满足，也可以不预订)并发送带有贷款详细信息的电子邮件。在这种情况下，域名看起来如何或者你用什么库/供应商来发送邮件并不重要。它说的是必须发生的事情，而不是如何发生。

## 基础设施服务

这是你离本垒最近的地方。基础设施层中的服务是实现来自域层的接口的服务。通知服务就是一个很好的例子。在域层，你定义一个我们想要的动作接口，例如 sendEmailAboutLoan，我们在这一层实现它。这是发送电子邮件的确切含义，并决定我们将使用哪个提供商或绑定特定于基础架构的错误。这是一个你实现库或者任何依赖于我们在程序中使用的库的地方。

您在应用层使用服务，但是，您的程序应该选择哪种直接实现更合适。

## **一些解释**

这样，您可以在不同的情况下使用不同的实现。为什么这么重要？假设我们的应用程序使用 PostgreSQL 或 MySQL 这样的关系数据库。它工作起来很神奇，但是您决定从应用层向 ApplicationService 添加一个单元测试。如果在同一个类中实现了与数据库相关的操作，那么就有问题了——您需要一个单独的数据库来进行测试，因为您不想将它们与本地数据混合在一起。在这种情况下，您有几种解决方法:

**在你需要的每个测试中创建整个数据库，并在最后删除它**

优点:

*   您有与生产环境非常相似的测试
*   您可以测试服务中的整个流程，并确信一切正常

缺点:

*   它们非常慢。在每个测试中删除和重新创建表会显著增加执行时间
*   如果您试图测试的类工作正常，但其他地方有问题，那么调试起来可能会很困难
*   参与项目的每个人(甚至是 CI 服务器)都必须有一个单独的测试数据库

如果你的应用程序需要一些其他的东西，比如 SMTP 服务器或其他软件，那就更成问题了。

**模仿直接访问数据库的方法或类**

这是一个更优雅的解决方案。模仿使你测试更快，并且独立于环境。在许多情况下，我看到这些与数据库相关的方法是私有的或受保护的，这很酷，因为作为用户，我不想知道它是如何工作的。我专注于它的功能。将这些方法组合在一起的对象也是如此。不幸的是，使用模拟有一些问题。你可以在类似于[为什么我不再使用模仿框架](http://www.tddfellow.com/blog/2016/06/21/why-i-dont-use-mocking-frameworks-anymore/)、[的文章中读到它们，当编写单元测试时，不要使用模仿](http://www.tddfellow.com/blog/2016/06/21/why-i-dont-use-mocking-frameworks-anymore/)或[来模仿或不模仿:这甚至是一个问题吗？](https://www.solutionsiq.com/resource/blog-post/to-mock-or-not-to-mock-is-that-even-a-question/)

**再加一层把我们的类和环境分开**

最后一个选择是将我们的类从环境中分离出来，这也是我们之前所做的。额外的层帮助还有另一个好处—您独立于环境。当您模仿 MySQL 服务器时，您知道您在测试中使用了 MySQL。在编写测试时，使用什么进行缓存并不重要。重要的是它能起作用。它使您能够每次都更改它，而不会对您的域代码产生任何影响。良好的分离有助于你保持项目中最重要的部分完全独立于你所使用的框架。你可以升级它的版本，而不用担心它会破坏你的核心代码。你甚至可以通过简单的剪切和粘贴来改变整个框架，只在几个地方集成整个项目。这使得维护更加容易。