---
title: 包括和排除类型 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: cbe6935e-2679-4b77-8914-a8d772240cf1
uid: core/modeling/included-types
ms.openlocfilehash: f533b24312af37634ce4957e43c39ce776bf0bf0
ms.sourcegitcommit: 87fcaba46535aa351db4bdb1231bd14b40e459b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2019
ms.locfileid: "59929792"
---
# <a name="including--excluding-types"></a><span data-ttu-id="187c4-102">包括和排除类型</span><span class="sxs-lookup"><span data-stu-id="187c4-102">Including & Excluding Types</span></span>

<span data-ttu-id="187c4-103">将类型包括到模型中意味着，EF 会有该类型的元数据，并且会尝试从数据库读取实例，以及将实例写入到数据库中。</span><span class="sxs-lookup"><span data-stu-id="187c4-103">Including a type in the model means that EF has metadata about that type and will attempt to read and write instances from/to the database.</span></span>

## <a name="conventions"></a><span data-ttu-id="187c4-104">约定</span><span class="sxs-lookup"><span data-stu-id="187c4-104">Conventions</span></span>

<span data-ttu-id="187c4-105">按照约定，在上下文的 `DbSet` 属性中公开的类型会包括在模型中。</span><span class="sxs-lookup"><span data-stu-id="187c4-105">By convention, types that are exposed in `DbSet` properties on your context are included in your model.</span></span> <span data-ttu-id="187c4-106">此外，在 `OnModelCreating` 方法中提及的类型也将包括在其中。</span><span class="sxs-lookup"><span data-stu-id="187c4-106">In addition, types that are mentioned in the `OnModelCreating` method are also included.</span></span> <span data-ttu-id="187c4-107">最后，通过以递归方式浏览已发现类型的导航属性而找到的任何类型也会包括在模型中。</span><span class="sxs-lookup"><span data-stu-id="187c4-107">Finally, any types that are found by recursively exploring the navigation properties of discovered types are also included in the model.</span></span>

<span data-ttu-id="187c4-108">**例如，以下代码列表中发现所有三种类型：**</span><span class="sxs-lookup"><span data-stu-id="187c4-108">**For example, in the following code listing all three types are discovered:**</span></span>

* <span data-ttu-id="187c4-109">`Blog`，因为它是在上下文的 `DbSet` 属性中公开的</span><span class="sxs-lookup"><span data-stu-id="187c4-109">`Blog` because it is exposed in a `DbSet` property on the context</span></span>

* <span data-ttu-id="187c4-110">`Post`，因为它是通过 `Blog.Posts` 导航属性发现的</span><span class="sxs-lookup"><span data-stu-id="187c4-110">`Post` because it is discovered via the `Blog.Posts` navigation property</span></span>

* <span data-ttu-id="187c4-111">`AuditEntry`，因为它在 `OnModelCreating` 中提及</span><span class="sxs-lookup"><span data-stu-id="187c4-111">`AuditEntry` because it is mentioned in `OnModelCreating`</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/Samples/IncludedTypes.cs?highlight=3,7,16)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<AuditEntry>();
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public List<Post> Posts { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog { get; set; }
}

public class AuditEntry
{
    public int AuditEntryId { get; set; }
    public string Username { get; set; }
    public string Action { get; set; }
}
```

## <a name="data-annotations"></a><span data-ttu-id="187c4-112">数据注释</span><span class="sxs-lookup"><span data-stu-id="187c4-112">Data Annotations</span></span>

<span data-ttu-id="187c4-113">可以使用数据注释来从模型中排除类型。</span><span class="sxs-lookup"><span data-stu-id="187c4-113">You can use Data Annotations to exclude a type from the model.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/IgnoreType.cs?highlight=20)]

## <a name="fluent-api"></a><span data-ttu-id="187c4-114">Fluent API</span><span class="sxs-lookup"><span data-stu-id="187c4-114">Fluent API</span></span>

<span data-ttu-id="187c4-115">Fluent API 可用于从模型中排除类型。</span><span class="sxs-lookup"><span data-stu-id="187c4-115">You can use the Fluent API to exclude a type from the model.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/IgnoreType.cs?highlight=12)]
