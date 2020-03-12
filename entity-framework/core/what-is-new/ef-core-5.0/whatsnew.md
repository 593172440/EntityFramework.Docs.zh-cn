---
title: EF Core 5.0 中的新增功能
author: ajcvickers
ms.date: 01/29/2020
uid: core/what-is-new/ef-core-5.0/whatsnew.md
ms.openlocfilehash: 65d7bd43e8a00c77fd6091a74c677635710d03e3
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413845"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="b827b-102">EF Core 5.0 中的新增功能</span><span class="sxs-lookup"><span data-stu-id="b827b-102">What's New in EF Core 5.0</span></span>

<span data-ttu-id="b827b-103">EF Core 5.0 目前正在开发中。</span><span class="sxs-lookup"><span data-stu-id="b827b-103">EF Core 5.0 is currently in development.</span></span>
<span data-ttu-id="b827b-104">此页面将包含每个预览版中引入的令人关注的更改的简要介绍。</span><span class="sxs-lookup"><span data-stu-id="b827b-104">This page will contain an overview of interesting changes introduced in each preview.</span></span>
<span data-ttu-id="b827b-105">EF Core 5.0 的第一个预览版初步预计将在 2020 年的第一个季度发布。</span><span class="sxs-lookup"><span data-stu-id="b827b-105">The first preview of EF Core 5.0 is tentatively expected in in the first quarter of 2020.</span></span>

<span data-ttu-id="b827b-106">此页面不会复制 [EF Core 5.0](plan.md) 的计划。</span><span class="sxs-lookup"><span data-stu-id="b827b-106">This page does not duplicate the [plan for EF Core 5.0](plan.md).</span></span>
<span data-ttu-id="b827b-107">计划中介绍了 EF Core 5.0 的整体主题，其中包括我们在交付最终版本之前打算包含的所有内容。</span><span class="sxs-lookup"><span data-stu-id="b827b-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="b827b-108">发布时，我们会将此处的链接添加到官方文档。</span><span class="sxs-lookup"><span data-stu-id="b827b-108">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-1-not-yet-shipped"></a><span data-ttu-id="b827b-109">预览版 1（尚未发布）</span><span class="sxs-lookup"><span data-stu-id="b827b-109">Preview 1 (Not yet shipped)</span></span>

### <a name="simple-logging"></a><span data-ttu-id="b827b-110">简单的日志记录</span><span class="sxs-lookup"><span data-stu-id="b827b-110">Simple logging</span></span>

<span data-ttu-id="b827b-111">此特性会添加类似于 EF6 中 `Database.Log` 的功能。</span><span class="sxs-lookup"><span data-stu-id="b827b-111">This feature adds functionality similar to `Database.Log` in EF6.</span></span>
<span data-ttu-id="b827b-112">也就是说，它提供了一种从 EF Core 获取日志的简单方法，而不需要配置任何类型的外部日志记录框架。</span><span class="sxs-lookup"><span data-stu-id="b827b-112">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="b827b-113">初步文档包含在 [2019 年 12 月 5 日发布的 EF 每周状态](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)中。</span><span class="sxs-lookup"><span data-stu-id="b827b-113">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="b827b-114">其他文档可通过问题 [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="b827b-114">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="b827b-115">获取生成的 SQL 的简单方法</span><span class="sxs-lookup"><span data-stu-id="b827b-115">Simple way to get generated SQL</span></span>

<span data-ttu-id="b827b-116">EF Core 5.0 引入了 `ToQueryString` 扩展方法，该方法会返回执行 LINQ 查询时 EF Core 生成的 SQL。</span><span class="sxs-lookup"><span data-stu-id="b827b-116">EF Core 5.0 introduces the `ToQueryString` extension method which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="b827b-117">初步文档包含在 [2020 年 1 月 9 日发布的 EF 每周状态](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246)中。</span><span class="sxs-lookup"><span data-stu-id="b827b-117">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="b827b-118">其他文档可通过问题 [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="b827b-118">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="b827b-119">增强的调试视图</span><span class="sxs-lookup"><span data-stu-id="b827b-119">Enhanced debug views</span></span>

<span data-ttu-id="b827b-120">调试视图是调试问题时查看 EF Core 内部情况的一种简单方法。</span><span class="sxs-lookup"><span data-stu-id="b827b-120">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span>
<span data-ttu-id="b827b-121">在一段时间之前，我们就已经实现了模型的调试视图。</span><span class="sxs-lookup"><span data-stu-id="b827b-121">A debug view for the Model was implemented some time ago.</span></span>
<span data-ttu-id="b827b-122">对于 EF Core 5.0，我们使模型视图更易于读取，并在状态管理器中为跟踪的实体添加了新的调试视图。</span><span class="sxs-lookup"><span data-stu-id="b827b-122">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="b827b-123">初步文档包含在 [2019 年 12 月 12 日发布的 EF 每周状态](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206)中。</span><span class="sxs-lookup"><span data-stu-id="b827b-123">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="b827b-124">其他文档可通过问题 [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="b827b-124">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="b827b-125">可在初始化的 DbContext 上更改连接或连接字符串</span><span class="sxs-lookup"><span data-stu-id="b827b-125">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="b827b-126">现在可以更轻松地创建 DbContext 实例，而无需任何连接或连接字符串。</span><span class="sxs-lookup"><span data-stu-id="b827b-126">It is now easier to create a DbContext instance without any connection or connection string.</span></span>
<span data-ttu-id="b827b-127">而且，现在可以在上下文实例上更改连接或连接字符串。</span><span class="sxs-lookup"><span data-stu-id="b827b-127">Also, the connection or connection string can now be mutated on the context instance.</span></span>
<span data-ttu-id="b827b-128">这使得同一个上下文实例能够动态地连接到不同的数据库。</span><span class="sxs-lookup"><span data-stu-id="b827b-128">This allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="b827b-129">文档可通过问题 [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="b827b-129">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="b827b-130">更改跟踪代理</span><span class="sxs-lookup"><span data-stu-id="b827b-130">Change-tracking proxies</span></span>

<span data-ttu-id="b827b-131">EF Core 现在可以生成自动实现 [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) 和 [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1) 的运行时代理。</span><span class="sxs-lookup"><span data-stu-id="b827b-131">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) and [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span></span>
<span data-ttu-id="b827b-132">这些代理会将实体属性的值更改直接报告给 EF Core，从而无需扫描更改。</span><span class="sxs-lookup"><span data-stu-id="b827b-132">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span>
<span data-ttu-id="b827b-133">不过，代理有其自身的一组限制，因此并不适合所有人使用。</span><span class="sxs-lookup"><span data-stu-id="b827b-133">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="b827b-134">文档可通过问题 [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="b827b-134">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="b827b-135">改进了对数据库 null 语义的处理</span><span class="sxs-lookup"><span data-stu-id="b827b-135">Improved handling of database null semantics</span></span>

<span data-ttu-id="b827b-136">关系数据库通常将 NULL 视为未知值，因此它不等于任何其他 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="b827b-136">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span>
<span data-ttu-id="b827b-137">另一方面，C# 将 null 视为与其他任何 null 相比均相等的定义值。</span><span class="sxs-lookup"><span data-stu-id="b827b-137">C#, on the other hand, treats null as a defined value which compares equal to any other null.</span></span>
<span data-ttu-id="b827b-138">默认情况下，EF Core 会转换查询，使查询使用 C# null 语义。</span><span class="sxs-lookup"><span data-stu-id="b827b-138">EF Core by default translates queries so that they use C# null semantics.</span></span>
<span data-ttu-id="b827b-139">EF Core 5.0 极大地提升了这些转换操作的效率。</span><span class="sxs-lookup"><span data-stu-id="b827b-139">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="b827b-140">文档可通过问题 [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="b827b-140">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="b827b-141">索引器属性</span><span class="sxs-lookup"><span data-stu-id="b827b-141">Indexer properties</span></span>

<span data-ttu-id="b827b-142">EF Core 5.0 支持 C# 索引器属性的映射。</span><span class="sxs-lookup"><span data-stu-id="b827b-142">EF Core 5.0 supports mapping of C# indexer properties.</span></span>
<span data-ttu-id="b827b-143">这使得实体能够充当属性包，实体中的列被映射为包中的命名属性。</span><span class="sxs-lookup"><span data-stu-id="b827b-143">This allows entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="b827b-144">文档可通过问题 [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="b827b-144">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="b827b-145">为枚举映射生成检查约束</span><span class="sxs-lookup"><span data-stu-id="b827b-145">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="b827b-146">EF Core 5.0 迁移现可为枚举属性映射生成检查约束。</span><span class="sxs-lookup"><span data-stu-id="b827b-146">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span>
<span data-ttu-id="b827b-147">例如：</span><span class="sxs-lookup"><span data-stu-id="b827b-147">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="b827b-148">文档可通过问题 [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="b827b-148">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="b827b-149">查询转换以获取更多 DateTime 构造</span><span class="sxs-lookup"><span data-stu-id="b827b-149">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="b827b-150">现在，包含新 DataTime 构造的查询会被转换。</span><span class="sxs-lookup"><span data-stu-id="b827b-150">Queries containing new DataTime construction are now translated.</span></span>
<span data-ttu-id="b827b-151">此外，SQL Server 函数 DateDiffWeek 现已映射。</span><span class="sxs-lookup"><span data-stu-id="b827b-151">Also, the SQL Server function DateDiffWeek is now mapped.</span></span>

<span data-ttu-id="b827b-152">文档可通过问题 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="b827b-152">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="b827b-153">查询转换以获取更多字节数组构造</span><span class="sxs-lookup"><span data-stu-id="b827b-153">Query translations for more byte array constructs</span></span>

<span data-ttu-id="b827b-154">现在，使用 Contains、Length、SequenceEqual 等对 byte[] 属性进行的查询会转换成 SQL。</span><span class="sxs-lookup"><span data-stu-id="b827b-154">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="b827b-155">初步文档包含在 [2019 年 12 月 5 日发布的 EF 每周状态](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)中。</span><span class="sxs-lookup"><span data-stu-id="b827b-155">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="b827b-156">更多文档可通过问题 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="b827b-156">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="b827b-157">反向查询转换</span><span class="sxs-lookup"><span data-stu-id="b827b-157">Query translation for Reverse</span></span>

<span data-ttu-id="b827b-158">现在，使用 `Reverse` 的查询会被转换。</span><span class="sxs-lookup"><span data-stu-id="b827b-158">Queries using `Reverse` are now translated.</span></span>
<span data-ttu-id="b827b-159">例如：</span><span class="sxs-lookup"><span data-stu-id="b827b-159">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="b827b-160">文档可通过问题 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="b827b-160">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="b827b-161">按位运算符的查询转换</span><span class="sxs-lookup"><span data-stu-id="b827b-161">Query translation for bitwise operators</span></span>

<span data-ttu-id="b827b-162">现在，使用按位运算符的查询会在更多的情况下被转换，例如：</span><span class="sxs-lookup"><span data-stu-id="b827b-162">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="b827b-163">文档可通过问题 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="b827b-163">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="b827b-164">Cosmos 上的字符串的查询转换</span><span class="sxs-lookup"><span data-stu-id="b827b-164">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="b827b-165">现在，在使用 Azure Cosmos DB 提供程序的情况下，使用字符串方法 Contains、StartsWith 和 EndsWith 的查询将被转换。</span><span class="sxs-lookup"><span data-stu-id="b827b-165">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="b827b-166">文档可通过问题 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="b827b-166">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
