---
title: 表拆分-EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 04/10/2019
ms.assetid: 0EC2CCE1-BD55-45D8-9EA9-20634987F094
uid: core/modeling/table-splitting
ms.openlocfilehash: a3a2e5842a6c6b4b490084d205a0d44bb46c17ee
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73656031"
---
# <a name="table-splitting"></a><span data-ttu-id="b194f-102">表拆分</span><span class="sxs-lookup"><span data-stu-id="b194f-102">Table Splitting</span></span>

>[!NOTE]
> <span data-ttu-id="b194f-103">此功能是 EF Core 2.0 中新增的功能。</span><span class="sxs-lookup"><span data-stu-id="b194f-103">This feature is new in EF Core 2.0.</span></span>

<span data-ttu-id="b194f-104">EF Core 允许将两个或多个实体映射到单个行。</span><span class="sxs-lookup"><span data-stu-id="b194f-104">EF Core allows to map two or more entities to a single row.</span></span> <span data-ttu-id="b194f-105">这称为 "_表拆分_" 或 "_表共享_"。</span><span class="sxs-lookup"><span data-stu-id="b194f-105">This is called _table splitting_ or _table sharing_.</span></span>

## <a name="configuration"></a><span data-ttu-id="b194f-106">配置</span><span class="sxs-lookup"><span data-stu-id="b194f-106">Configuration</span></span>

<span data-ttu-id="b194f-107">若要使用表拆分，则需要将多个实体类型映射到同一个表中，将主键映射到相同的列，并且在同一个实体类型的主键与另一个实体类型的主键之间配置至少一个关系。</span><span class="sxs-lookup"><span data-stu-id="b194f-107">To use table splitting the entity types need to be mapped to the same table, have the primary keys mapped to the same columns and at least one relationship configured between the primary key of one entity type and another in the same table.</span></span>

<span data-ttu-id="b194f-108">表拆分的常见方案是只使用表中的部分列，以获得更好的性能或封装。</span><span class="sxs-lookup"><span data-stu-id="b194f-108">A common scenario for table splitting is using only a subset of the columns in the table for greater performance or encapsulation.</span></span>

<span data-ttu-id="b194f-109">在此示例中 `Order` 表示 `DetailedOrder`的子集。</span><span class="sxs-lookup"><span data-stu-id="b194f-109">In this example `Order` represents a subset of `DetailedOrder`.</span></span>

[!code-csharp[Order](../../../samples/core/Modeling/TableSplitting/Order.cs?name=Order)]

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/TableSplitting/DetailedOrder.cs?name=DetailedOrder)]

<span data-ttu-id="b194f-110">除了所需的配置之外，我们还 `Property(o => o.Status).HasColumnName("Status")` 将 `DetailedOrder.Status` 映射到与 `Order.Status`相同的列。</span><span class="sxs-lookup"><span data-stu-id="b194f-110">In addition to the required configuration we call `Property(o => o.Status).HasColumnName("Status")` to map `DetailedOrder.Status` to the same column as `Order.Status`.</span></span>

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=TableSplitting&highlight=3)]

> [!TIP]
> <span data-ttu-id="b194f-111">有关更多上下文，请参阅[完整的示例项目](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting)。</span><span class="sxs-lookup"><span data-stu-id="b194f-111">See the [full sample project](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting) for more context.</span></span>

## <a name="usage"></a><span data-ttu-id="b194f-112">用法</span><span class="sxs-lookup"><span data-stu-id="b194f-112">Usage</span></span>

<span data-ttu-id="b194f-113">使用表拆分来保存和查询实体的方式与处理其他实体的方式相同。</span><span class="sxs-lookup"><span data-stu-id="b194f-113">Saving and querying entities using table splitting is done in the same way as other entities.</span></span> <span data-ttu-id="b194f-114">从 EF Core 3.0 开始，可以将依赖实体引用 `null`。</span><span class="sxs-lookup"><span data-stu-id="b194f-114">And starting with EF Core 3.0 the dependent entity reference can be `null`.</span></span> <span data-ttu-id="b194f-115">如果依赖实体使用的所有列都 `NULL` 数据库，则查询时将不会创建该数据库的实例。</span><span class="sxs-lookup"><span data-stu-id="b194f-115">If all of the columns used by the dependent entity are `NULL` is the database then no instance for it will be created when queried.</span></span> <span data-ttu-id="b194f-116">这也会导致所有属性都是可选的并且设置为 `null`，这可能不是预期的。</span><span class="sxs-lookup"><span data-stu-id="b194f-116">This would also happen all of the properties are optional and set to `null`, which might not be expected.</span></span>

[!code-csharp[Usage](../../../samples/core/Modeling/TableSplitting/Program.cs?name=Usage)]

## <a name="concurrency-tokens"></a><span data-ttu-id="b194f-117">并发令牌</span><span class="sxs-lookup"><span data-stu-id="b194f-117">Concurrency tokens</span></span>

<span data-ttu-id="b194f-118">如果共享表的任何实体类型都具有并发标记，则必须将其包含在所有其他实体类型中，以避免在仅更新映射到同一个表的一个实体时出现过时并发令牌值。</span><span class="sxs-lookup"><span data-stu-id="b194f-118">If any of the entity types sharing a table has a concurrency token then it must be included in all other entity types to avoid a stale concurrency token value when only one of the entities mapped to the same table is updated.</span></span>

<span data-ttu-id="b194f-119">为避免向使用代码公开它，可以在影子状态中创建一个。</span><span class="sxs-lookup"><span data-stu-id="b194f-119">To avoid exposing it to the consuming code it's possible the create one in shadow-state.</span></span>

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=ConcurrencyToken&highlight=2)]
