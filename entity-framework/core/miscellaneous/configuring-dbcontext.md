---
title: 配置 DbContext 的 EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: d7a22b5a-4c5b-4e3b-9897-4d7320fcd13f
uid: core/miscellaneous/configuring-dbcontext
ms.openlocfilehash: 316d363d4a1b8a909efc1c32b492280c0d16cb4e
ms.sourcegitcommit: 960e42a01b3a2f76da82e074f64f52252a8afecc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2019
ms.locfileid: "65405210"
---
# <a name="configuring-a-dbcontext"></a>配置 DbContext

本文演示通过 `DbContextOptions` 配置 `DbContext` 以使用特定的 EF Core 提供程序和可选行为来连接到数据库的基本模式。

## <a name="design-time-dbcontext-configuration"></a>设计时 DbContext 配置

EF Core 设计时工具（如[迁移](xref:core/managing-schemas/migrations/index)）需要能够发现和创建 `DbContext` 类型的工作实例，以便收集有关应用程序实体类型及其如何映射到数据库架构的详细信息。 此过程可以自动执行，只要该工具可以轻松创建 `DbContext` 并使其与在运行时采用相似的配置方式。

尽管为 `DbContext` 提供必要配置信息的任何模式都可以在运行时工作，但需要在设计时使用 `DbContext`的工具仅适用于有限数量的模式。“设计时上下文创建”部分包含对这些内容的详细介绍。 [“设计时上下文创建”](xref:core/miscellaneous/cli/dbcontext-creation)部分包含对这些内容的详细介绍。

## <a name="configuring-dbcontextoptions"></a>配置 DbContextOptions

`DbContext` 必须具有 `DbContextOptions` 的实例才能执行工作。 `DbContextOptions` 实例包含如下配置信息：

- 数据库提供程序，若要使用，通常选择通过调用的方法，如`UseSqlServer`或`UseSqlite`。 这些扩展方法需要相应的提供程序包，如`Microsoft.EntityFrameworkCore.SqlServer`或`Microsoft.EntityFrameworkCore.Sqlite`。 中定义的方法`Microsoft.EntityFrameworkCore`命名空间。
- 任何必要的连接字符串或标识符的数据库实例中，通常作为参数传递到上述提供程序选择方法
- 任何提供程序级别的可选行为选择器，通常还链接到提供程序选择方法调用中
- 任何常规 EF Core 行为选择器，通常链接之后或之前提供程序选择器方法

下面的示例将配置`DbContextOptions`若要使用 SQL Server 提供程序，在连接包含`connectionString`变量、 提供程序级别的命令超时，以及可使在中执行的所有查询 EF Core 行为选择器`DbContext`[否跟踪](xref:core/querying/tracking#no-tracking-queries)默认情况下：

``` csharp
optionsBuilder
    .UseSqlServer(connectionString, providerOptions=>providerOptions.CommandTimeout(60))
    .UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
```

> [!NOTE]  
> 上面提到的提供程序选择器方法和其他行为选择器方法是 `DbContextOptions` 或特定于提供程序的选项类上的扩展方法。 若要访问这些扩展方法，可能需要具有定义域内的命名空间（通常为 `Microsoft.EntityFrameworkCore`）并在项目中包含其他包依赖关系。

`DbContextOptions`可以提供给`DbContext`通过重写`OnConfiguring`方法或构造函数参数通过从外部。

如果将使用它们，`OnConfiguring`最后应用，并且可以覆盖选项提供给构造函数参数。

### <a name="constructor-argument"></a>构造函数参数

使用构造函数的上下文代码：

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
        : base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

> [!TIP]  
> DbContext 基构造函数还接受非泛型版本的`DbContextOptions`，但不是建议使用多个上下文类型的应用程序使用的非泛型版本。

从构造函数自变量进行初始化的应用程序代码：

``` csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a>OnConfiguring

上下文代码`OnConfiguring`:

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlite("Data Source=blog.db");
    }
}
```

应用程序代码来初始化`DbContext`，它使用`OnConfiguring`:

``` csharp
using (var context = new BloggingContext())
{
  // do stuff
}
```

> [!TIP]
> 此方法不会将自身添加到测试，除非测试以完整的数据库为目标。

### <a name="using-dbcontext-with-dependency-injection"></a>使用依赖关系注入使用 DbContext

EF Core 支持使用`DbContext`与依赖关系注入容器。 DbContext 类型可以通过使用添加到服务容器`AddDbContext<TContext>`方法。

`AddDbContext<TContext>` 将这两个 DbContext 类型， `TContext`，并相应`DbContextOptions<TContext>`可用于从服务容器的注入。

请参阅[多个读取](#more-reading)以下依赖关系注入的其他信息。

添加`Dbcontext`依赖关系注入到：

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

这要求将添加[构造函数参数](#constructor-argument)到 DbContext 类型接受`DbContextOptions<TContext>`。

上下文代码：

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
      :base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

（在 ASP.NET Core) 的应用程序代码：

``` csharp
public class MyController
{
    private readonly BloggingContext _context;

    public MyController(BloggingContext context)
    {
      _context = context;
    }

    ...
}
```

（服务提供商处直接使用，不太常见） 的应用程序代码：

``` csharp
using (var context = serviceProvider.GetService<BloggingContext>())
{
  // do stuff
}

var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
```
## <a name="avoiding-dbcontext-threading-issues"></a>避免 DbContext 线程处理问题

Entity Framework Core 不支持在同一个正在运行的多个并行操作`DbContext`实例。 这包括并行执行异步查询和任何显式的并发使用，从多个线程。 因此，始终`await`异步调用立即，或使用不同`DbContext`并行执行的操作的实例。

EF Core 时检测到尝试使用`DbContext`实例同时，您将看到`InvalidOperationException`与类似这样的消息： 

> 在此上下文中的上一个操作完成之前启动的第二个操作。 这通常被由于由不同的线程使用 DbContext 的同一个实例，但是保证实例成员都不是线程安全。

当未检测到出现的并发访问时，它可能导致未定义的行为，应用程序崩溃和数据损坏。

可以在同一个 inadvernetly 原因并发访问的常见错误有`DbContext`实例：

### <a name="forgetting-to-await-the-completion-of-an-asynchronous-operation-before-starting-any-other-operation-on-the-same-dbcontext"></a>忘记 await 的异步操作开始对相同 DbContext 的任何其他操作之前完成

异步方法使 EF Core 启动非阻塞方式访问数据库的操作。 但是，如果调用方不等待完成的其中一个方法，并继续执行其他操作`DbContext`，则状态的`DbContext`则可以为 （而且很可能将） 已损坏。 

始终立即等待 EF Core 异步方法。  

### <a name="implicitly-sharing-dbcontext-instances-across-multiple-threads-via-dependency-injection"></a>隐式在通过依赖关系注入的多个线程间共享 DbContext 实例

[ `AddDbContext` ](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)扩展方法注册`DbContext`类型与[作用域的生存期](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes)默认情况下。 

这是安全的 ASP.NET Core 应用程序中的并发访问问题，因为只有一个线程在给定时间执行每个客户端请求并且每个请求获取单独的依赖关系注入作用域 (并因此单独`DbContext`实例）。

但是显式并行执行多个线程的任何代码应确保`DbContext`实例不是曾经 accesed 并发。

使用依赖关系注入，这可以通过实现作为其作用域内和创建作用域注册上下文 (使用`IServiceScopeFactory`) 为每个线程，或者注册`DbContext`为瞬态 (使用的重载`AddDbContext`接受`ServiceLifetime`参数)。

## <a name="more-reading"></a>详细阅读

* 阅读 [ASP.NET Core 入门](../get-started/aspnetcore/index.md)，了解有关配合使用 ASP.NET Core 和 EF 的详细信息。
* 阅读[依赖关系注入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)，了解有关使用 DI 的详细信息。
* 阅读[测试](testing/index.md)了解详细信息。
