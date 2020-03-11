---
title: 从以前的版本升级到 EF Core 2-EF Core
author: divega
ms.date: 08/13/2017
ms.assetid: 8BD43C8C-63D9-4F3A-B954-7BC518A1B7DB
uid: core/miscellaneous/1x-2x-upgrade
ms.openlocfilehash: b27c09fdb6210dd7c6aa0c8bc912a8bd183c16b9
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414229"
---
# <a name="upgrading-applications-from-previous-versions-to-ef-core-20"></a>从以前版本的应用程序升级到 EF Core 2.0

我们已在2.0 中利用了显著改进现有 Api 和行为的机会。 有一些改进可能需要修改现有应用程序代码，但我们认为，对于大多数应用程序而言，这种影响会很低，在大多数情况下，只需重新编译和最少的指导更改即可替换已过时的 Api。

更新现有应用程序到 EF Core 2.0 可能还需要：

1. 将应用程序的目标 .NET 实现升级到支持 .NET Standard 2.0 的应用程序。 有关更多详细信息，请参阅[支持的 .Net 实现](xref:core/platforms/index)。

2. 标识的提供程序与 EF Core 2.0 兼容的目标数据库。 请参阅下面[的 EF Core 2.0 需要2.0 数据库提供商](#ef-core-20-requires-a-20-database-provider)。

3. 升级到 2.0 所有 EF Core 包 （运行时和工具）。 有关更多详细信息，请参阅[安装 EF Core](xref:core/get-started/install/index) 。

4. 进行任何必要的代码更改，以弥补本文档其余部分所述的重大更改。

## <a name="aspnet-core-now-includes-ef-core"></a>ASP.NET Core 现在包括 EF Core

面向 ASP.NET Core 2.0 的应用程序可以使用 EF Core 2.0，而不需要第三方数据库提供程序以外的其他依赖项。 但是，面向以前版本的 ASP.NET Core 应用程序需要为了使用 EF Core 2.0 升级到 ASP.NET Core 2.0。 有关将 ASP.NET Core 应用程序升级到2.0 的更多详细信息，请参阅[主题中的 ASP.NET Core 文档](/aspnet/core/migration/1x-to-2x/)。

## <a name="new-way-of-getting-application-services-in-aspnet-core"></a>在 ASP.NET Core 中获取应用程序服务的新方法

已为 2.0 中断 1.x 中使用的 EF Core 的设计时逻辑的方式更新的建议的模式为 ASP.NET Core web 应用程序。 在设计时，EF Core 将尝试直接调用 `Startup.ConfigureServices` 以便访问应用程序的服务提供程序。 在 ASP.NET Core 2.0 中，将在 `Startup` 类之外初始化配置。 使用 EF Core 的应用程序通常通过配置访问其连接字符串，因此 `Startup` 本身就不再足够了。 如果升级 ASP.NET Core 1.x 应用程序，你可能会收到以下错误，使用 EF Core 工具时。

> 未在 "ApplicationContext" 上找到任何无参数的构造函数。 将无参数构造函数添加到 "ApplicationContext"，或在与 "ApplicationContext" 相同的程序集中添加 "IDesignTimeDbContextFactory&lt;ApplicationContext&gt;" 的实现

ASP.NET Core 2.0 的默认模板中已添加新的设计时挂钩。 静态 `Program.BuildWebHost` 方法使 EF Core 能够在设计时访问应用程序的服务提供程序。 如果要升级 ASP.NET Core 1.x 应用程序，则需要将 `Program` 类更新为类似于以下内容。

``` csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;

namespace AspNetCoreDotNetCore2._0App
{
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
}
```

强烈建议在将应用程序更新到2.0 时采用这一新模式，这是为了使 Entity Framework Core 迁移等产品功能有效。 另一种常见方法是[实现*IDesignTimeDbContextFactory\<TContext >* ](xref:core/miscellaneous/cli/dbcontext-creation#from-a-design-time-factory)。

## <a name="idbcontextfactory-renamed"></a>已重命名 IDbContextFactory

为了支持不同的应用程序模式并使用户能够更好地控制其 `DbContext` 在设计时的使用方式，我们在过去提供 `IDbContextFactory<TContext>` 接口。 在设计时，EF Core 工具将发现项目中此接口的实现，并使用它来创建 `DbContext` 对象。

此接口具有一个非常通用的名称，此名称误导某些用户尝试重复使用它来执行其他 `DbContext`创建的方案。 当 EF 工具在设计时尝试使用其实现并引发 `Update-Database` 或 `dotnet ef database update` 之类的命令失败时，它们被捕获到 "保护"。

为了传达此接口的强设计时语义，我们已将其重命名为 `IDesignTimeDbContextFactory<TContext>`。

对于2.0 版，`IDbContextFactory<TContext>` 仍存在，但被标记为过时。

## <a name="dbcontextfactoryoptions-removed"></a>DbContextFactoryOptions 已删除

由于上述 ASP.NET Core 2.0 更改，我们发现新 `IDesignTimeDbContextFactory<TContext>` 接口上不再需要 `DbContextFactoryOptions`。 下面是你应改用的替代方法。

| DbContextFactoryOptions | 替代项                                                  |
|:------------------------|:-------------------------------------------------------------|
| ApplicationBasePath     | AppContext.BaseDirectory                                     |
| ContentRootPath         | Directory.GetCurrentDirectory()                              |
| EnvironmentName         | Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") |

## <a name="design-time-working-directory-changed"></a>设计时工作目录已更改

ASP.NET Core 2.0 更改还要求 `dotnet ef` 使用的工作目录与 Visual Studio 在运行应用程序时使用的工作目录一致。 这种情况的一个显而易见的副作用是，SQLite 文件名现在是相对于项目目录的，而不是相对于其使用的输出目录。

## <a name="ef-core-20-requires-a-20-database-provider"></a>EF Core 2.0 需要 2.0 版数据库提供程序

为使用 EF Core 2.0 我们所做工作许多简化和方式的数据库提供程序中的改进。 这意味着 1.0.x 版和 1.1.x 提供程序不会使用 EF Core 2.0。

SQL Server 和 SQLite 提供程序由 EF 团队提供，2.0 版本将作为2.0 版本的一部分提供。 正在为2.0 更新面向[SQL Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact)、 [PostgreSQL](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL)和[MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql)的开源第三方提供程序。 对于所有其他提供商，请与提供商联系。

## <a name="logging-and-diagnostics-events-have-changed"></a>日志记录和诊断事件已更改

注意：这些更改不应影响大部分应用程序代码。

发送到[ILogger](/dotnet/api/microsoft.extensions.logging.ilogger)的消息的事件 id 在2.0 中发生了更改。 现在，事件 ID 在 EF Core 代码内具有唯一性。 这些消息现在还遵循 MVC 等所用的结构化日志记录的标准模式。

记录器类别也已更改。 现提供通过 [DbLoggerCategory](https://github.com/aspnet/EntityFrameworkCore/blob/rel/2.0.0/src/EFCore/DbLoggerCategory.cs) 访问的熟知类别集。

[DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md)事件现在使用与对应 `ILogger` 消息相同的事件 ID 名称。 事件负载是派生自[EventData](/dotnet/api/microsoft.entityframeworkcore.diagnostics.eventdata)的所有名义类型。

事件 Id、负载类型和类别记录在[CoreEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.coreeventid)和[RelationalEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.relationaleventid)类中。

Id 还会从 Microsoft.entityframeworkcore 移动到新的 Microsoft.entityframeworkcore 命名空间。

## <a name="ef-core-relational-metadata-api-changes"></a>EF Core 关系元数据 API 更改

EF Core 2.0 现将对所用的每个不同提供程序生成不同的 [IModel](/dotnet/api/microsoft.entityframeworkcore.metadata.imodel)。 这对应用程序而言通常是透明的。 这样一来，就可以简化较低级别的元数据 Api，使对_公共关系元数据概念_的任何访问始终通过调用 `.Relational` 而不是 `.SqlServer`、`.Sqlite`等。例如，1.1. x 代码如下：

``` csharp
var tableName = context.Model.FindEntityType(typeof(User)).SqlServer().TableName;
```

现在应如下所示：

``` csharp
var tableName = context.Model.FindEntityType(typeof(User)).Relational().TableName;
```

现在可使用扩展方法（而不是 `ForSqlServerToTable`方法），根据当前使用的提供程序来编写条件代码。 例如：

```csharp
modelBuilder.Entity<User>().ToTable(
    Database.IsSqlServer() ? "SqlServerName" : "OtherName");
```

请注意，此更改仅适用于为_所有_关系提供程序定义的 api/元数据。 当 API 和元数据仅特定于单个提供程序时，它保持不变。 例如，聚集索引特定于 SQL server，因此仍必须使用 `ForSqlServerIsClustered` 和 `.SqlServer().IsClustered()`。

## <a name="dont-take-control-of-the-ef-service-provider"></a>不控制 EF 服务提供程序

EF Core 使用内部 `IServiceProvider` （依赖关系注入容器）来实现其内部实现。 应用程序应允许 EF Core 以创建和管理在特殊情况下此提供程序除外。 请考虑删除对 `UseInternalServiceProvider`的任何调用。 如果应用程序需要调用 `UseInternalServiceProvider`，请考虑将[问题存档](https://github.com/aspnet/EntityFramework/Issues)，以便我们能够调查处理方案的其他方法。

除非还调用了 `UseInternalServiceProvider`，否则应用程序代码不需要调用 `AddEntityFramework`、`AddEntityFrameworkSqlServer`等。 删除对 `AddEntityFramework` 或 `AddEntityFrameworkSqlServer`等的任何现有调用，`AddDbContext` 仍以与以前相同的方式使用。

## <a name="in-memory-databases-must-be-named"></a>内存中数据库必须命名为

全局未命名内存中数据库已删除，而所有内存中数据库必须命名为。 例如：

``` csharp
optionsBuilder.UseInMemoryDatabase("MyDatabase");
```

这将创建/使用名为 "MyDatabase" 的数据库。 如果使用相同的名称再次调用 `UseInMemoryDatabase`，则将使用相同的内存中数据库，以允许多个上下文实例共享它。

## <a name="read-only-api-changes"></a>只读 API 更改

`IsReadOnlyBeforeSave`、`IsReadOnlyAfterSave`和 `IsStoreGeneratedAlways` 已弃用并已替换为[BeforeSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.beforesavebehavior)和[AfterSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.aftersavebehavior)。 这些行为适用于任何属性（而不仅是存储生成的属性），并确定在插入到数据库行（`BeforeSaveBehavior`）或更新现有数据库行（`AfterSaveBehavior`）时应如何使用属性的值。

标记为[ValueGenerated. OnAddOrUpdate](/dotnet/api/microsoft.entityframeworkcore.metadata.valuegenerated) （例如，对于计算列）的属性将默认忽略当前在属性上设置的任何值。 这意味着，无论是否已对所跟踪的实体设置或修改任何值，都将始终获取存储生成的值。 可以通过设置其他 `Before\AfterSaveBehavior`来更改此设置。

## <a name="new-clientsetnull-delete-behavior"></a>新 ClientSetNull 删除行为

在以前的版本中， [DeleteBehavior](/dotnet/api/microsoft.entityframeworkcore.deletebehavior)具有与上下文跟踪的实体的行为，这些实体与 `SetNull` 语义更的已关闭匹配。 在 EF Core 2.0 中，引入了新的 `ClientSetNull` 行为作为可选关系的默认行为。 此行为对于使用 EF Core 创建的数据库，具有跟踪实体和 `Restrict` 行为的 `SetNull` 语义。 在我们的经验中，这是跟踪的实体和数据库的最预期/有用的行为。 为可选关系设置时，`DeleteBehavior.Restrict` 现在适用于跟踪的实体。

## <a name="provider-design-time-packages-removed"></a>已删除提供程序设计时包

`Microsoft.EntityFrameworkCore.Relational.Design` 包已删除。 它的内容已合并到 `Microsoft.EntityFrameworkCore.Relational` 和 `Microsoft.EntityFrameworkCore.Design`中。

这会传播到提供程序的设计时包。 将删除这些包（`Microsoft.EntityFrameworkCore.Sqlite.Design`、`Microsoft.EntityFrameworkCore.SqlServer.Design`等），并将其内容合并到主提供程序包中。

若要在 EF Core 2.0 中启用 `Scaffold-DbContext` 或 `dotnet ef dbcontext scaffold`，只需引用单个提供程序包：

``` xml
<PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer"
    Version="2.0.0" />
<PackageReference Include="Microsoft.EntityFrameworkCore.Tools"
    Version="2.0.0"
    PrivateAssets="All" />
<DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet"
    Version="2.0.0" />
```
