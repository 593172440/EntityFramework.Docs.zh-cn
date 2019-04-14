---
title: 测试以及 InMemory 的 EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 0d0590f1-1ea3-4d5c-8f44-db17395cd3f3
uid: core/miscellaneous/testing/in-memory
ms.openlocfilehash: 8aaea52f22954ef6a2b7d9b9c5627597c61ac644
ms.sourcegitcommit: 8f801993c9b8cd8a8fbfa7134818a8edca79e31a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2019
ms.locfileid: "59562541"
---
# <a name="testing-with-inmemory"></a><span data-ttu-id="90ade-102">使用 InMemory 进行测试</span><span class="sxs-lookup"><span data-stu-id="90ade-102">Testing with InMemory</span></span>

<span data-ttu-id="90ade-103">InMemory 提供程序时，你想要测试组件使用类似于连接真实数据库，而无需实际的数据库操作的开销。</span><span class="sxs-lookup"><span data-stu-id="90ade-103">The InMemory provider is useful when you want to test components using something that approximates connecting to the real database, without the overhead of actual database operations.</span></span>

> [!TIP]  
> <span data-ttu-id="90ade-104">可在 GitHub 上查看此文章的[示例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing)。</span><span class="sxs-lookup"><span data-stu-id="90ade-104">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing) on GitHub.</span></span>

## <a name="inmemory-is-not-a-relational-database"></a><span data-ttu-id="90ade-105">InMemory 不是关系数据库</span><span class="sxs-lookup"><span data-stu-id="90ade-105">InMemory is not a relational database</span></span>

<span data-ttu-id="90ade-106">EF Core 数据库提供程序不需要是关系数据库。</span><span class="sxs-lookup"><span data-stu-id="90ade-106">EF Core database providers do not have to be relational databases.</span></span> <span data-ttu-id="90ade-107">InMemory 设计为通用数据库进行测试，而不是要模拟关系数据库。</span><span class="sxs-lookup"><span data-stu-id="90ade-107">InMemory is designed to be a general purpose database for testing, and is not designed to mimic a relational database.</span></span>

<span data-ttu-id="90ade-108">包括一些示例：</span><span class="sxs-lookup"><span data-stu-id="90ade-108">Some examples of this include:</span></span>

* <span data-ttu-id="90ade-109">InMemory 将允许您将会违反引用完整性约束关系数据库中的数据保存。</span><span class="sxs-lookup"><span data-stu-id="90ade-109">InMemory will allow you to save data that would violate referential integrity constraints in a relational database.</span></span>
* <span data-ttu-id="90ade-110">如果在模型中的属性使用 DefaultValueSql(string)，这是一个关系数据库 API，不会影响针对 InMemory 运行时。</span><span class="sxs-lookup"><span data-stu-id="90ade-110">If you use DefaultValueSql(string) for a property in your model, this is a relational database API and will have no effect when running against InMemory.</span></span>
* <span data-ttu-id="90ade-111">[通过时间戳/行版本的并发](xref:core/modeling/concurrency#timestamprow-version)(`[Timestamp]`或`IsRowVersion`) 不受支持。</span><span class="sxs-lookup"><span data-stu-id="90ade-111">[Concurrency via Timestamp/row version](xref:core/modeling/concurrency#timestamprow-version) (`[Timestamp]` or `IsRowVersion`) is not supported.</span></span> <span data-ttu-id="90ade-112">否[DbUpdateConcurrencyException](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception)如果更新执行此操作将引发使用旧的并发标记。</span><span class="sxs-lookup"><span data-stu-id="90ade-112">No [DbUpdateConcurrencyException](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception) will be thrown if an update is done using an old concurrency token.</span></span>

> [!TIP]  
> <span data-ttu-id="90ade-113">对于许多测试目的这些差异将不起作用。</span><span class="sxs-lookup"><span data-stu-id="90ade-113">For many test purposes these differences will not matter.</span></span> <span data-ttu-id="90ade-114">但是，如果你想要针对的表现得更像真正的关系数据库进行测试，请考虑使用[SQLite 内存中模式](sqlite.md)。</span><span class="sxs-lookup"><span data-stu-id="90ade-114">However, if you want to test against something that behaves more like a true relational database, then consider using [SQLite in-memory mode](sqlite.md).</span></span>

## <a name="example-testing-scenario"></a><span data-ttu-id="90ade-115">示例测试方案</span><span class="sxs-lookup"><span data-stu-id="90ade-115">Example testing scenario</span></span>

<span data-ttu-id="90ade-116">请考虑以下允许应用程序代码执行一些与博客相关的操作的服务。</span><span class="sxs-lookup"><span data-stu-id="90ade-116">Consider the following service that allows application code to perform some operations related to blogs.</span></span> <span data-ttu-id="90ade-117">该服务在内部使用连接到 SQL Server 数据库的 `DbContext`。</span><span class="sxs-lookup"><span data-stu-id="90ade-117">Internally it uses a `DbContext` that connects to a SQL Server database.</span></span> <span data-ttu-id="90ade-118">可以用来交换此上下文，以便我们可以编写此服务的有效测试，而无需修改代码，或执行大量工作来创建测试连接到 InMemory 数据库上下文的双精度。</span><span class="sxs-lookup"><span data-stu-id="90ade-118">It would be useful to swap this context to connect to an InMemory database so that we can write efficient tests for this service without having to modify the code, or do a lot of work to create a test double of the context.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BlogService.cs)]

## <a name="get-your-context-ready"></a><span data-ttu-id="90ade-119">准备您的上下文</span><span class="sxs-lookup"><span data-stu-id="90ade-119">Get your context ready</span></span>

### <a name="avoid-configuring-two-database-providers"></a><span data-ttu-id="90ade-120">避免配置两个数据库提供程序</span><span class="sxs-lookup"><span data-stu-id="90ade-120">Avoid configuring two database providers</span></span>

<span data-ttu-id="90ade-121">在测试中，从外部配置上下文以使用 InMemory 提供程序。</span><span class="sxs-lookup"><span data-stu-id="90ade-121">In your tests you are going to externally configure the context to use the InMemory provider.</span></span> <span data-ttu-id="90ade-122">如果要通过在上下文中替代 `OnConfiguring` 来配置数据库提供程序，则需要添加一些条件代码，从而确保只有在尚未配置数据库提供程序的情况下才进行配置。</span><span class="sxs-lookup"><span data-stu-id="90ade-122">If you are configuring a database provider by overriding `OnConfiguring` in your context, then you need to add some conditional code to ensure that you only configure the database provider if one has not already been configured.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#OnConfiguring)]

> [!TIP]  
> <span data-ttu-id="90ade-123">如果你使用的 ASP.NET Core，则应不需要此代码由于外部 （会在 Startup.cs) 的上下文已配置数据库提供程序。</span><span class="sxs-lookup"><span data-stu-id="90ade-123">If you are using ASP.NET Core, then you should not need this code since your database provider is already configured outside of the context (in Startup.cs).</span></span>

### <a name="add-a-constructor-for-testing"></a><span data-ttu-id="90ade-124">添加用于测试的构造函数</span><span class="sxs-lookup"><span data-stu-id="90ade-124">Add a constructor for testing</span></span>

<span data-ttu-id="90ade-125">若要启用针对不同的数据库的测试的最简单方法是修改上下文，以公开一个构造函数接受`DbContextOptions<TContext>`。</span><span class="sxs-lookup"><span data-stu-id="90ade-125">The simplest way to enable testing against a different database is to modify your context to expose a constructor that accepts a `DbContextOptions<TContext>`.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#Constructors)]

> [!TIP]  
> <span data-ttu-id="90ade-126">`DbContextOptions<TContext>` 告知上下文其所有的设置，例如要连接的数据库。</span><span class="sxs-lookup"><span data-stu-id="90ade-126">`DbContextOptions<TContext>` tells the context all of its settings, such as which database to connect to.</span></span> <span data-ttu-id="90ade-127">这与在上下文中运行 OnConfiguring 方法所生成的对象相同。</span><span class="sxs-lookup"><span data-stu-id="90ade-127">This is the same object that is built by running the OnConfiguring method in your context.</span></span>

## <a name="writing-tests"></a><span data-ttu-id="90ade-128">编写测试</span><span class="sxs-lookup"><span data-stu-id="90ade-128">Writing tests</span></span>

<span data-ttu-id="90ade-129">使用此提供程序进行测试的关键是可以让使用 InMemory 提供程序，并控制内存中数据库的作用域的上下文。</span><span class="sxs-lookup"><span data-stu-id="90ade-129">The key to testing with this provider is the ability to tell the context to use the InMemory provider, and control the scope of the in-memory database.</span></span> <span data-ttu-id="90ade-130">通常，每个测试方法都需要一个干净的数据库。</span><span class="sxs-lookup"><span data-stu-id="90ade-130">Typically you want a clean database for each test method.</span></span>

<span data-ttu-id="90ade-131">下面是使用 InMemory 数据库的测试类的示例。</span><span class="sxs-lookup"><span data-stu-id="90ade-131">Here is an example of a test class that uses the InMemory database.</span></span> <span data-ttu-id="90ade-132">每个测试方法指定一个唯一的数据库名称，表示每个方法都有其自己的 InMemory 数据库。</span><span class="sxs-lookup"><span data-stu-id="90ade-132">Each test method specifies a unique database name, meaning each method has its own InMemory database.</span></span>

>[!TIP]
> <span data-ttu-id="90ade-133">若要使用`.UseInMemoryDatabase()`扩展方法，引用的 NuGet 包[Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)。</span><span class="sxs-lookup"><span data-stu-id="90ade-133">To use the `.UseInMemoryDatabase()` extension method, reference the NuGet package [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/TestProject/InMemory/BlogServiceTests.cs)]
