---
title: 继承（关系数据库）-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 9a7c5488-aaf4-4b40-b1ff-f435ff30f6ec
uid: core/modeling/relational/inheritance
ms.openlocfilehash: 381d1878007bb78b359eb49649f4356f1e5eb04a
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655631"
---
# <a name="inheritance-relational-database"></a>继承（关系数据库）

> [!NOTE]  
> 一般而言，本部分中的配置适用于关系数据库。 安装关系数据库提供程序时，此处显示的扩展方法将变为可用（原因在于共享的 Microsoft.EntityFrameworkCore.Relational 包）。

EF 模型中的继承用于控制如何在数据库中表示实体类中的继承。

> [!NOTE]  
> 目前，EF Core 中仅实现了每个层次结构一个表（TPH）模式。 其他常见模式（例如每种类型一个表（TPT））和每个具体的表类型（TPC）尚不可用。

## <a name="conventions"></a>约定

按照约定，将使用每个层次结构一个表（TPH）模式映射继承。 TPH 使用单个表来存储层次结构中所有类型的数据。 鉴别器列用于标识每行所表示的类型。

如果模型中显式包括两个或更多个继承类型，EF Core 将仅设置继承（有关更多详细信息，请参阅[继承](../inheritance.md)）。

下面的示例演示了一个简单的继承方案，以及使用 TPH 模式存储在关系数据库表中的数据。 *鉴别*器列标识每个行中存储哪种类型的*博客*。

[!code-csharp[Main](../../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs#Model)]

![图像](_static/inheritance-tph-data.png)

>[!NOTE]
> 使用 TPH 映射时，数据库列会根据需要自动进行为 null。

## <a name="data-annotations"></a>数据注释

不能使用数据批注来配置继承。

## <a name="fluent-api"></a>Fluent API

您可以使用熟知的 API 来配置鉴别器列的名称和类型，以及用于标识层次结构中的每个类型的值。

[!code-csharp[Main](../../../../samples/core/Modeling/FluentAPI/InheritanceTPHDiscriminator.cs#Inheritance)]

## <a name="configuring-the-discriminator-property"></a>配置鉴别器属性

在上面的示例中，将在层次结构的基实体上将鉴别器创建为[影子属性](xref:core/modeling/shadow-properties)。 由于它是模型中的属性，因此可以像配置其他属性一样对其进行配置。 例如，若要设置默认的、按约定的鉴别器正在使用的最大长度，请执行以下操作：

```C#
modelBuilder.Entity<Blog>()
    .Property("Discriminator")
    .HasMaxLength(200);
```

鉴别器还可以映射到实体中的实际 CLR 属性。 例如:

```C#
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .HasDiscriminator<string>("BlogType");
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
    public string BlogType { get; set; }
}

public class RssBlog : Blog
{
    public string RssUrl { get; set; }
}
```

将这两个内容组合在一起可以将鉴别器映射到实际属性并对其进行配置：

```C#
modelBuilder.Entity<Blog>(b =>
{
    b.HasDiscriminator<string>("BlogType");

    b.Property(e => e.BlogType)
        .HasMaxLength(200)
        .HasColumnName("blog_type");
});
```
