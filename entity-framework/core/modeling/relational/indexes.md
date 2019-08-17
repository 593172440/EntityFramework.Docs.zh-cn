---
title: 索引的 EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 4581e7ba-5e7f-452c-9937-0aaf790ba10a
uid: core/modeling/relational/indexes
ms.openlocfilehash: dfada7446f812f3c277572cc1338441272e8f448
ms.sourcegitcommit: 7b7f774a5966b20d2aed5435a672a1edbe73b6fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2019
ms.locfileid: "69565365"
---
# <a name="indexes"></a>索引

> [!NOTE]  
> 一般而言，本部分中的配置适用于关系数据库。 安装关系数据库提供程序后，此处显示的扩展方法将变为可用（原因在于 Microsoft.EntityFrameworkCore.Relational 包是共享的）。

关系数据库中的索引映射到与实体框架核心中的索引相同的概念。

## <a name="conventions"></a>约定

按照约定, 索引命名为`IX_<type name>_<property name>`。 对于复合索引`<property name>` , 将成为以下划线分隔的属性名称列表。

## <a name="data-annotations"></a>数据注释

不能使用数据批注配置索引。

## <a name="fluent-api"></a>Fluent API

您可以使用熟知的 API 来配置索引的名称。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/IndexName.cs?name=Model&highlight=9)]

您还可以指定筛选器。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/IndexFilter.cs?name=Model&highlight=9)]

使用 SQL Server 提供程序 EF 为唯一索引中包含的所有可以为 null 的列添加 "IS NOT NULL" 筛选器。 若要重写此约定, 可以`null`提供一个值。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/IndexNoFilter.cs?name=Model&highlight=10)]

### <a name="include-columns-in-sql-server-indexes"></a>在 SQL Server 索引中包含列

当查询中的所有列都作为键列或非键列包含在索引中时, 可以[通过包含列配置索引](https://docs.microsoft.com/sql/relational-databases/indexes/create-indexes-with-included-columns)以显著提高查询性能。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/Samples/Relational/ForSqlServerHasIndex.cs?name=Model)]
