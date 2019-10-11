---
title: 从 EF Core 1.0 RC1 升级到 RC2-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 6d75b229-cc79-4d08-88cd-3a1c1b24d88f
uid: core/miscellaneous/rc1-rc2-upgrade
ms.openlocfilehash: 887b7cd539b9c0f5a680398f5039757420228710
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181277"
---
# <a name="upgrading-from-ef-core-10-rc1-to-10-rc2"></a>从 EF Core 1.0 RC1 升级到 1.0 RC2

本文介绍如何将使用 RC1 包生成的应用程序移动到 RC2。

## <a name="package-names-and-versions"></a>包名称和版本

在 RC1 和 RC2 之间，我们将从 "实体框架 7" 改为 "Entity Framework Core"。 可以[通过 Scott Hanselman 详细了解此帖子](https://www.hanselman.com/blog/ASPNET5IsDeadIntroducingASPNETCore10AndNETCore10.aspx)中的更改原因。 由于此更改，我们的包名称从 @no__t 更改为 `Microsoft.EntityFrameworkCore.*`，版本从 `7.0.0-rc1-final` 更改为 `1.0.0-rc2-final` （对于工具为 `1.0.0-preview1-final`）。

**需要完全删除 RC1 包，然后安装 RC2 包。** 下面是一些常用包的映射。

| RC1 包                                               | RC2 等效项                                                       |
|:----------------------------------------------------------|:---------------------------------------------------------------------|
| EntityFramework.MicrosoftSqlServer        7.0.0-rc1-final | Microsoft.EntityFrameworkCore.SqlServer         1.0.0-rc2-final      |
| EntityFramework.SQLite                    7.0.0-rc1-final | Microsoft.EntityFrameworkCore.Sqlite            1.0.0-rc2-final      |
| EntityFramework7.Npgsql                   3.1.0-rc1-3     | NpgSql.EntityFrameworkCore.Postgres             <to be advised>      |
| EntityFramework.SqlServerCompact35        7.0.0-rc1-final | EntityFrameworkCore.SqlServerCompact35          1.0.0-rc2-final      |
| EntityFramework.SqlServerCompact40        7.0.0-rc1-final | EntityFrameworkCore.SqlServerCompact40          1.0.0-rc2-final      |
| EntityFramework 7.0.0 版-rc1-最终 | Microsoft.EntityFrameworkCore.InMemory          1.0.0-rc2-final      |
| EntityFramework.IBMDataServer             7.0.0-beta1     | 尚不适用于 RC2                                            |
| EntityFramework.Commands                  7.0.0-rc1-final | Microsoft.EntityFrameworkCore.Tools             1.0.0-preview1-final |
| EntityFramework.MicrosoftSqlServer.Design 7.0.0-rc1-final | Microsoft.EntityFrameworkCore.SqlServer.Design  1.0.0-rc2-final      |

## <a name="namespaces"></a>命名空间

除了包名称外，命名空间从 @no__t 更改为 @no__t。 您可以使用 `using Microsoft.EntityFrameworkCore` 的 @no__t 的查找/替换来处理此更改。

## <a name="table-naming-convention-changes"></a>表命名约定更改

RC2 中的一个重要功能更改是将给定实体的 @no__t 的名称指定为其映射到的表名，而不只是类名。 有关此更改的详细信息，请参阅[相关公告问题](https://github.com/aspnet/Announcements/issues/167)。

对于现有的 RC1 应用程序，建议将以下代码添加到 `OnModelCreating` 方法的开头，以保留 RC1 命名策略：

``` csharp
foreach (var entity in modelBuilder.Model.GetEntityTypes())
{
    entity.Relational().TableName = entity.DisplayName();
}
```

如果要采用新的命名策略，我们建议成功完成其余的升级步骤，并删除代码并创建迁移，以应用表重命名。

## <a name="adddbcontext--startupcs-changes-aspnet-core-projects-only"></a>AddDbContext / Startup.cs 更改 （仅适用于 ASP.NET Core 项目）

在 RC1 中，你必须将实体框架服务添加到应用程序服务提供程序中 `Startup.ConfigureServices(...)`：

``` csharp
services.AddEntityFramework()
  .AddSqlServer()
  .AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

在 RC2 中，你可以删除对 `AddEntityFramework()`、`AddSqlServer()` 等的调用：

``` csharp
services.AddDbContext<ApplicationDbContext>(options =>
  options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

还需要向派生上下文添加一个构造函数，该构造函数采用上下文选项，并将其传递到基构造函数。 这是必需的，因为我们消除了在幕后 snuck 它们的一些可怕的神奇之处：

``` csharp
public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
    : base(options)
{
}
```

## <a name="passing-in-an-iserviceprovider"></a>传入 IServiceProvider

如果你的 RC1 代码将 `IServiceProvider` 传递到上下文，此代码现在已移动到 @no__t，而不是单独的构造函数参数。 使用 @no__t 设置服务提供程序。

### <a name="testing"></a>正在测试

执行此操作的最常见方案是在测试时控制 InMemory 数据库的范围。 有关使用 RC2 执行此操作的示例，请参阅更新的[测试](testing/index.md)文章。

### <a name="resolving-internal-services-from-application-service-provider-aspnet-core-projects-only"></a>解析内部服务从应用程序服务提供商 （仅适用于 ASP.NET Core 项目）

如果你有一个 ASP.NET Core 应用并且想 EF 若要解决应用程序服务提供商提供的内部服务，则的重载`AddDbContext`，可以对此进行配置：

``` csharp
services.AddEntityFrameworkSqlServer()
  .AddDbContext<ApplicationDbContext>((serviceProvider, options) =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"])
           .UseInternalServiceProvider(serviceProvider));
```

> [!WARNING]  
> 建议允许 EF 内部管理其自己的服务，除非您有理由将内部 EF 服务合并到您的应用程序服务提供程序中。 你可能想要执行此操作的主要原因是使用应用程序服务提供程序来替换 EF 在内部使用的服务

## <a name="dnx-commands--net-cli-aspnet-core-projects-only"></a>DNX 命令 = >.NET CLI （仅适用于 ASP.NET Core 项目）

如果先前对 ASP.NET 5 项目使用了 `dnx ef` 命令，则这些命令现在已移动到 @no__t 1 命令。 相同的命令语法仍适用。 对于语法信息，可以使用 `dotnet ef --help`。

命令注册的方式在 RC2 中发生了变化，因为 DNX 被 .NET CLI 替换。 现在，命令在 `project.json` 的 @no__t 的部分中注册：

``` json
"tools": {
  "Microsoft.EntityFrameworkCore.Tools": {
    "version": "1.0.0-preview1-final",
    "imports": [
      "portable-net45+win8+dnxcore50",
      "portable-net45+win8"
    ]
  }
}
```

> [!TIP]  
> 如果你使用 Visual Studio，你现在可以使用程序包管理器控制台运行 ASP.NET Core 项目 （这不支持在 RC1 中） 的 EF 命令。 你仍需要在 `project.json` 的 `tools` 部分注册命令来执行此操作。

## <a name="package-manager-commands-require-powershell-5"></a>程序包管理器命令需要 PowerShell 5

如果你在 Visual Studio 中的包管理器控制台中使用实体框架命令，则需要确保已安装 PowerShell 5。 这是将在下一版本中删除的临时要求（有关详细信息，请参阅[问题 #5327](https://github.com/aspnet/EntityFramework/issues/5327) ）。

## <a name="using-imports-in-projectjson"></a>在项目中使用 "imports"

一些 EF Core 依赖关系不支持.NET 标准尚未。 标准.NET 和.NET Core 项目中的 EF Core 可能会要求添加"导入"到 project.json 临时的解决方法。

添加 EF 时，NuGet 还原将显示以下错误消息：

``` Console
Package Ix-Async 1.2.5 is not compatible with netcoreapp1.0 (.NETCoreApp,Version=v1.0). Package Ix-Async 1.2.5 supports:
  - net40 (.NETFramework,Version=v4.0)
  - net45 (.NETFramework,Version=v4.5)
  - portable-net45+win8+wp8 (.NETPortable,Version=v0.0,Profile=Profile78)
Package Remotion.Linq 2.0.2 is not compatible with netcoreapp1.0 (.NETCoreApp,Version=v1.0). Package Remotion.Linq 2.0.2 supports:
  - net35 (.NETFramework,Version=v3.5)
  - net40 (.NETFramework,Version=v4.0)
  - net45 (.NETFramework,Version=v4.5)
  - portable-net45+win8+wp8+wpa81 (.NETPortable,Version=v0.0,Profile=Profile259)
```

解决方法是手动导入可移植配置文件 "net451 + win8"。 这会强制 NuGet 将与此提供的二进制文件相匹配的二进制文件视为与 .NET Standard 兼容的框架，即使它们不是这样。 尽管 "net451 + win8" 与 .NET Standard 的兼容性不是 100%，但其兼容性足以用于从 PCL 转换到 .NET Standard。 当 EF 的依赖项最终升级到 .NET Standard 时，可以删除导入。

可以在数组语法中将多个框架添加到 "导入"。 如果将其他库添加到项目，则可能需要其他导入。

``` json
{
  "frameworks": {
    "netcoreapp1.0": {
      "imports": ["dnxcore50", "portable-net451+win8"]
    }
  }
}
```

请参阅[问题 #5176](https://github.com/aspnet/EntityFramework/issues/5176)。
