---
title: 使用属性值-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: e3278b4b-9378-4fdb-923d-f64d80aaae70
ms.openlocfilehash: d8a18182754980d79b71df3f227b30c4ce40366f
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182144"
---
# <a name="working-with-property-values"></a><span data-ttu-id="258ad-102">使用属性值</span><span class="sxs-lookup"><span data-stu-id="258ad-102">Working with property values</span></span>
<span data-ttu-id="258ad-103">大多数情况下实体框架将负责跟踪实体实例的属性的状态、原始值和当前值。</span><span class="sxs-lookup"><span data-stu-id="258ad-103">For the most part Entity Framework will take care of tracking the state, original values, and current values of the properties of your entity instances.</span></span> <span data-ttu-id="258ad-104">但是，在某些情况下（例如，已断开连接的情况下），你希望查看或操作有关属性的信息 EF。</span><span class="sxs-lookup"><span data-stu-id="258ad-104">However, there may be some cases - such as disconnected scenarios - where you want to view or manipulate the information EF has about the properties.</span></span> <span data-ttu-id="258ad-105">本主题所介绍的方法同样适用于查询使用 Code First 和 EF 设计器创建的模型。</span><span class="sxs-lookup"><span data-stu-id="258ad-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="258ad-106">实体框架跟踪所跟踪实体的每个属性的两个值。</span><span class="sxs-lookup"><span data-stu-id="258ad-106">Entity Framework keeps track of two values for each property of a tracked entity.</span></span> <span data-ttu-id="258ad-107">当前值为，如名称所示，是实体中属性的当前值。</span><span class="sxs-lookup"><span data-stu-id="258ad-107">The current value is, as the name indicates, the current value of the property in the entity.</span></span> <span data-ttu-id="258ad-108">原始值是从数据库中查询实体或将实体附加到上下文时属性所具有的值。</span><span class="sxs-lookup"><span data-stu-id="258ad-108">The original value is the value that the property had when the entity was queried from the database or attached to the context.</span></span>  

<span data-ttu-id="258ad-109">使用属性值的一般机制有两种：</span><span class="sxs-lookup"><span data-stu-id="258ad-109">There are two general mechanisms for working with property values:</span></span>  

- <span data-ttu-id="258ad-110">单个属性的值可以使用属性方法以强类型方式获取。</span><span class="sxs-lookup"><span data-stu-id="258ad-110">The value of a single property can be obtained in a strongly typed way using the Property method.</span></span>  
- <span data-ttu-id="258ad-111">实体的所有属性的值都可以读取到 DbPropertyValues 对象中。</span><span class="sxs-lookup"><span data-stu-id="258ad-111">Values for all properties of an entity can be read into a DbPropertyValues object.</span></span> <span data-ttu-id="258ad-112">然后，DbPropertyValues 充当类似字典的对象，以允许读取和设置属性值。</span><span class="sxs-lookup"><span data-stu-id="258ad-112">DbPropertyValues then acts as a dictionary-like object to allow property values to be read and set.</span></span> <span data-ttu-id="258ad-113">DbPropertyValues 对象中的值可以从其他 DbPropertyValues 对象中的值或其他某个对象的值进行设置，如实体的另一个副本或简单的数据传输对象（DTO）。</span><span class="sxs-lookup"><span data-stu-id="258ad-113">The values in a DbPropertyValues object can be set from values in another DbPropertyValues object or from values in some other object, such as another copy of the entity or a simple data transfer object (DTO).</span></span>  

<span data-ttu-id="258ad-114">以下部分显示了使用上述两种机制的示例。</span><span class="sxs-lookup"><span data-stu-id="258ad-114">The sections below show examples of using both of the above mechanisms.</span></span>  

## <a name="getting-and-setting-the-current-or-original-value-of-an-individual-property"></a><span data-ttu-id="258ad-115">获取和设置单个属性的当前值或原始值</span><span class="sxs-lookup"><span data-stu-id="258ad-115">Getting and setting the current or original value of an individual property</span></span>  

<span data-ttu-id="258ad-116">下面的示例演示如何读取属性的当前值，然后将其设置为新值：</span><span class="sxs-lookup"><span data-stu-id="258ad-116">The example below shows how the current value of a property can be read and then set to a new value:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(3);

    // Read the current value of the Name property
    string currentName1 = context.Entry(blog).Property(u => u.Name).CurrentValue;

    // Set the Name property to a new value
    context.Entry(blog).Property(u => u.Name).CurrentValue = "My Fancy Blog";

    // Read the current value of the Name property using a string for the property name
    object currentName2 = context.Entry(blog).Property("Name").CurrentValue;

    // Set the Name property to a new value using a string for the property name
    context.Entry(blog).Property("Name").CurrentValue = "My Boring Blog";
}
```  

<span data-ttu-id="258ad-117">使用 OriginalValue 属性而非 CurrentValue 属性来读取或设置原始值。</span><span class="sxs-lookup"><span data-stu-id="258ad-117">Use the OriginalValue property instead of the CurrentValue property to read or set the original value.</span></span>  

<span data-ttu-id="258ad-118">请注意，当使用字符串指定属性名时，返回的值将被类型化为 "object"。</span><span class="sxs-lookup"><span data-stu-id="258ad-118">Note that the returned value is typed as “object” when a string is used to specify the property name.</span></span> <span data-ttu-id="258ad-119">另一方面，如果使用 lambda 表达式，则返回值为强类型。</span><span class="sxs-lookup"><span data-stu-id="258ad-119">On the other hand, the returned value is strongly typed if a lambda expression is used.</span></span>  

<span data-ttu-id="258ad-120">如果新值不同于旧值，则将属性值设置为时，只会将属性标记为已修改。</span><span class="sxs-lookup"><span data-stu-id="258ad-120">Setting the property value like this will only mark the property as modified if the new value is different from the old value.</span></span>  

<span data-ttu-id="258ad-121">以这种方式设置属性值时，即使关闭了 AutoDetectChanges，也会自动检测更改。</span><span class="sxs-lookup"><span data-stu-id="258ad-121">When a property value is set in this way the change is automatically detected even if AutoDetectChanges is turned off.</span></span>  

## <a name="getting-and-setting-the-current-value-of-an-unmapped-property"></a><span data-ttu-id="258ad-122">获取和设置未映射的属性的当前值</span><span class="sxs-lookup"><span data-stu-id="258ad-122">Getting and setting the current value of an unmapped property</span></span>  

<span data-ttu-id="258ad-123">还可以读取未映射到数据库的属性的当前值。</span><span class="sxs-lookup"><span data-stu-id="258ad-123">The current value of a property that is not mapped to the database can also be read.</span></span> <span data-ttu-id="258ad-124">未映射的属性的一个示例可能是博客上的 .Rsslink 属性。</span><span class="sxs-lookup"><span data-stu-id="258ad-124">An example of an unmapped property could be an RssLink property on Blog.</span></span> <span data-ttu-id="258ad-125">此值可以基于 BlogId 进行计算，因此无需存储在数据库中。</span><span class="sxs-lookup"><span data-stu-id="258ad-125">This value may be calculated based on the BlogId, and therefore doesn't need to be stored in the database.</span></span> <span data-ttu-id="258ad-126">例如：</span><span class="sxs-lookup"><span data-stu-id="258ad-126">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    // Read the current value of an unmapped property
    var rssLink = context.Entry(blog).Property(p => p.RssLink).CurrentValue;

    // Use a string to specify the property name
    var rssLinkAgain = context.Entry(blog).Property("RssLink").CurrentValue;
}
```  

<span data-ttu-id="258ad-127">如果属性公开了 setter，还可以设置当前值。</span><span class="sxs-lookup"><span data-stu-id="258ad-127">The current value can also be set if the property exposes a setter.</span></span>  

<span data-ttu-id="258ad-128">在对未映射的属性执行实体框架验证时，读取未映射的属性的值非常有用。</span><span class="sxs-lookup"><span data-stu-id="258ad-128">Reading the values of unmapped properties is useful when performing Entity Framework validation of unmapped properties.</span></span> <span data-ttu-id="258ad-129">出于相同原因，可以读取当前值并将其设置为当前未由上下文跟踪的实体的属性。</span><span class="sxs-lookup"><span data-stu-id="258ad-129">For the same reason current values can be read and set for properties of entities that are not currently being tracked by the context.</span></span> <span data-ttu-id="258ad-130">例如：</span><span class="sxs-lookup"><span data-stu-id="258ad-130">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Create an entity that is not being tracked
    var blog = new Blog { Name = "ADO.NET Blog" };

    // Read and set the current value of Name as before
    var currentName1 = context.Entry(blog).Property(u => u.Name).CurrentValue;
    context.Entry(blog).Property(u => u.Name).CurrentValue = "My Fancy Blog";
    var currentName2 = context.Entry(blog).Property("Name").CurrentValue;
    context.Entry(blog).Property("Name").CurrentValue = "My Boring Blog";
}
```  

<span data-ttu-id="258ad-131">请注意，原始值不可用于未映射的属性或上下文未跟踪的实体属性。</span><span class="sxs-lookup"><span data-stu-id="258ad-131">Note that original values are not available for unmapped properties or for properties of entities that are not being tracked by the context.</span></span>  

## <a name="checking-whether-a-property-is-marked-as-modified"></a><span data-ttu-id="258ad-132">检查属性是否被标记为已修改</span><span class="sxs-lookup"><span data-stu-id="258ad-132">Checking whether a property is marked as modified</span></span>  

<span data-ttu-id="258ad-133">下面的示例演示如何检查单个属性是否被标记为已修改：</span><span class="sxs-lookup"><span data-stu-id="258ad-133">The example below shows how to check whether or not an individual property is marked as modified:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var nameIsModified1 = context.Entry(blog).Property(u => u.Name).IsModified;

    // Use a string for the property name
    var nameIsModified2 = context.Entry(blog).Property("Name").IsModified;
}
```  

<span data-ttu-id="258ad-134">调用 SaveChanges 时，已修改属性的值将作为更新发送到数据库。</span><span class="sxs-lookup"><span data-stu-id="258ad-134">The values of modified properties are sent as updates to the database when SaveChanges is called.</span></span>  

##  <a name="marking-a-property-as-modified"></a><span data-ttu-id="258ad-135">将属性标记为已修改</span><span class="sxs-lookup"><span data-stu-id="258ad-135">Marking a property as modified</span></span>  

<span data-ttu-id="258ad-136">下面的示例演示如何强制将单个属性标记为已修改：</span><span class="sxs-lookup"><span data-stu-id="258ad-136">The example below shows how to force an individual property to be marked as modified:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    context.Entry(blog).Property(u => u.Name).IsModified = true;

    // Use a string for the property name
    context.Entry(blog).Property("Name").IsModified = true;
}
```  

<span data-ttu-id="258ad-137">将属性标记为已修改会强制在调用 SaveChanges 时将更新发送到该属性的数据库，即使该属性的当前值与原始值相同也是如此。</span><span class="sxs-lookup"><span data-stu-id="258ad-137">Marking a property as modified forces an update to be send to the database for the property when SaveChanges is called even if the current value of the property is the same as its original value.</span></span>  

<span data-ttu-id="258ad-138">目前不能在标记为 "已修改" 后将单个属性重置为不修改。</span><span class="sxs-lookup"><span data-stu-id="258ad-138">It is not currently possible to reset an individual property to be not modified after it has been marked as modified.</span></span> <span data-ttu-id="258ad-139">这是我们计划在未来版本中提供支持的内容。</span><span class="sxs-lookup"><span data-stu-id="258ad-139">This is something we plan to support in a future release.</span></span>  

## <a name="reading-current-original-and-database-values-for-all-properties-of-an-entity"></a><span data-ttu-id="258ad-140">读取实体的所有属性的当前值、原始值和数据库值</span><span class="sxs-lookup"><span data-stu-id="258ad-140">Reading current, original, and database values for all properties of an entity</span></span>  

<span data-ttu-id="258ad-141">下面的示例演示如何在数据库中读取实体的所有映射属性的当前值、原始值和值。</span><span class="sxs-lookup"><span data-stu-id="258ad-141">The example below shows how to read the current values, the original values, and the values actually in the database for all mapped properties of an entity.</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Make a modification to Name in the tracked entity
    blog.Name = "My Cool Blog";

    // Make a modification to Name in the database
    context.Database.SqlCommand("update dbo.Blogs set Name = 'My Boring Blog' where Id = 1");

    // Print out current, original, and database values
    Console.WriteLine("Current values:");
    PrintValues(context.Entry(blog).CurrentValues);

    Console.WriteLine("\nOriginal values:");
    PrintValues(context.Entry(blog).OriginalValues);

    Console.WriteLine("\nDatabase values:");
    PrintValues(context.Entry(blog).GetDatabaseValues());
}

public static void PrintValues(DbPropertyValues values)
{
    foreach (var propertyName in values.PropertyNames)
    {
        Console.WriteLine("Property {0} has value {1}",
                          propertyName, values[propertyName]);
    }
}
```  

<span data-ttu-id="258ad-142">当前值是实体的属性当前包含的值。</span><span class="sxs-lookup"><span data-stu-id="258ad-142">The current values are the values that the properties of the entity currently contain.</span></span> <span data-ttu-id="258ad-143">原始值是查询实体时从数据库中读取的值。</span><span class="sxs-lookup"><span data-stu-id="258ad-143">The original values are the values that were read from the database when the entity was queried.</span></span> <span data-ttu-id="258ad-144">数据库值是当前存储在数据库中的值。</span><span class="sxs-lookup"><span data-stu-id="258ad-144">The database values are the values as they are currently stored in the database.</span></span> <span data-ttu-id="258ad-145">如果数据库中的值可能在查询实体后发生更改（例如，当另一个用户对数据库进行了并发编辑时），获取数据库值将很有用。</span><span class="sxs-lookup"><span data-stu-id="258ad-145">Getting the database values is useful when the values in the database may have changed since the entity was queried such as when a concurrent edit to the database has been made by another user.</span></span>  

## <a name="setting-current-or-original-values-from-another-object"></a><span data-ttu-id="258ad-146">设置其他对象的当前值或原始值</span><span class="sxs-lookup"><span data-stu-id="258ad-146">Setting current or original values from another object</span></span>  

<span data-ttu-id="258ad-147">可以通过从另一个对象复制值来更新已跟踪实体的当前值或原始值。</span><span class="sxs-lookup"><span data-stu-id="258ad-147">The current or original values of a tracked entity can be updated by copying values from another object.</span></span> <span data-ttu-id="258ad-148">例如：</span><span class="sxs-lookup"><span data-stu-id="258ad-148">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    var coolBlog = new Blog { Id = 1, Name = "My Cool Blog" };
    var boringBlog = new BlogDto { Id = 1, Name = "My Boring Blog" };

    // Change the current and original values by copying the values from other objects
    var entry = context.Entry(blog);
    entry.CurrentValues.SetValues(coolBlog);
    entry.OriginalValues.SetValues(boringBlog);

    // Print out current and original values
    Console.WriteLine("Current values:");
    PrintValues(entry.CurrentValues);

    Console.WriteLine("\nOriginal values:");
    PrintValues(entry.OriginalValues);
}

public class BlogDto
{
    public int Id { get; set; }
    public string Name { get; set; }
}
```  

<span data-ttu-id="258ad-149">运行上面的代码将输出：</span><span class="sxs-lookup"><span data-stu-id="258ad-149">Running the code above will print out:</span></span>  

```console
Current values:
Property Id has value 1
Property Name has value My Cool Blog

Original values:
Property Id has value 1
Property Name has value My Boring Blog
```  

<span data-ttu-id="258ad-150">当使用从服务调用或 n 层应用程序中的客户端获取的值更新实体时，有时会使用此方法。</span><span class="sxs-lookup"><span data-stu-id="258ad-150">This technique is sometimes used when updating an entity with values obtained from a service call or a client in an n-tier application.</span></span> <span data-ttu-id="258ad-151">请注意，所使用的对象不必与实体具有相同的类型，但前提是它具有与实体的名称相匹配的属性。</span><span class="sxs-lookup"><span data-stu-id="258ad-151">Note that the object used does not have to be of the same type as the entity so long as it has properties whose names match those of the entity.</span></span> <span data-ttu-id="258ad-152">在上面的示例中，BlogDTO 的实例用于更新原始值。</span><span class="sxs-lookup"><span data-stu-id="258ad-152">In the example above, an instance of BlogDTO is used to update the original values.</span></span>  

<span data-ttu-id="258ad-153">请注意，从其他对象复制时，仅将设置为不同值的属性标记为已修改。</span><span class="sxs-lookup"><span data-stu-id="258ad-153">Note that only properties that are set to different values when copied from the other object will be marked as modified.</span></span>  

## <a name="setting-current-or-original-values-from-a-dictionary"></a><span data-ttu-id="258ad-154">从字典设置当前值或原始值</span><span class="sxs-lookup"><span data-stu-id="258ad-154">Setting current or original values from a dictionary</span></span>  

<span data-ttu-id="258ad-155">可以通过从字典或其他一些数据结构复制值来更新已跟踪实体的当前值或原始值。</span><span class="sxs-lookup"><span data-stu-id="258ad-155">The current or original values of a tracked entity can be updated by copying values from a dictionary or some other data structure.</span></span> <span data-ttu-id="258ad-156">例如：</span><span class="sxs-lookup"><span data-stu-id="258ad-156">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var newValues = new Dictionary\<string, object>
    {
        { "Name", "The New ADO.NET Blog" },
        { "Url", "blogs.msdn.com/adonet" },
    };

    var currentValues = context.Entry(blog).CurrentValues;

    foreach (var propertyName in newValues.Keys)
    {
        currentValues[propertyName] = newValues[propertyName];
    }

    PrintValues(currentValues);
}
```  

<span data-ttu-id="258ad-157">使用 OriginalValues 属性而非 CurrentValues 属性来设置原始值。</span><span class="sxs-lookup"><span data-stu-id="258ad-157">Use the OriginalValues property instead of the CurrentValues property to set original values.</span></span>  

## <a name="setting-current-or-original-values-from-a-dictionary-using-property"></a><span data-ttu-id="258ad-158">使用属性设置字典中的当前值或原始值</span><span class="sxs-lookup"><span data-stu-id="258ad-158">Setting current or original values from a dictionary using Property</span></span>  

<span data-ttu-id="258ad-159">如上所述，使用 CurrentValues 或 OriginalValues 的一种替代方法是使用属性方法来设置每个属性的值。</span><span class="sxs-lookup"><span data-stu-id="258ad-159">An alternative to using CurrentValues or OriginalValues as shown above is to use the Property method to set the value of each property.</span></span> <span data-ttu-id="258ad-160">当你需要设置复杂属性的值时，这可能更好。</span><span class="sxs-lookup"><span data-stu-id="258ad-160">This can be preferable when you need to set the values of complex properties.</span></span> <span data-ttu-id="258ad-161">例如：</span><span class="sxs-lookup"><span data-stu-id="258ad-161">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    var newValues = new Dictionary\<string, object>
    {
        { "Name", "John Doe" },
        { "Location.City", "Redmond" },
        { "Location.State.Name", "Washington" },
        { "Location.State.Code", "WA" },
    };

    var entry = context.Entry(user);

    foreach (var propertyName in newValues.Keys)
    {
        entry.Property(propertyName).CurrentValue = newValues[propertyName];
    }
}
```  

<span data-ttu-id="258ad-162">在上面的示例中，使用点分名称访问复杂属性。</span><span class="sxs-lookup"><span data-stu-id="258ad-162">In the example above complex properties are accessed using dotted names.</span></span> <span data-ttu-id="258ad-163">有关访问复杂属性的其他方法，请参阅本主题后面有关复杂属性的两个部分。</span><span class="sxs-lookup"><span data-stu-id="258ad-163">For other ways to access complex properties see the two sections later in this topic specifically about complex properties.</span></span>  

## <a name="creating-a-cloned-object-containing-current-original-or-database-values"></a><span data-ttu-id="258ad-164">创建包含当前、原始或数据库值的克隆对象</span><span class="sxs-lookup"><span data-stu-id="258ad-164">Creating a cloned object containing current, original, or database values</span></span>  

<span data-ttu-id="258ad-165">从 CurrentValues、OriginalValues 或 GetDatabaseValues 返回的 DbPropertyValues 对象可用于创建实体的克隆。</span><span class="sxs-lookup"><span data-stu-id="258ad-165">The DbPropertyValues object returned from CurrentValues, OriginalValues, or GetDatabaseValues can be used to create a clone of the entity.</span></span> <span data-ttu-id="258ad-166">此克隆将包含 DbPropertyValues 对象中用于创建它的属性值。</span><span class="sxs-lookup"><span data-stu-id="258ad-166">This clone will contain the property values from the DbPropertyValues object used to create it.</span></span> <span data-ttu-id="258ad-167">例如：</span><span class="sxs-lookup"><span data-stu-id="258ad-167">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var clonedBlog = context.Entry(blog).GetDatabaseValues().ToObject();
}
```  

<span data-ttu-id="258ad-168">请注意，返回的对象不是实体，也不是由上下文跟踪。</span><span class="sxs-lookup"><span data-stu-id="258ad-168">Note that the object returned is not the entity and is not being tracked by the context.</span></span> <span data-ttu-id="258ad-169">返回的对象也不会将任何关系设置为其他对象。</span><span class="sxs-lookup"><span data-stu-id="258ad-169">The returned object also does not have any relationships set to other objects.</span></span>  

<span data-ttu-id="258ad-170">克隆的对象可用于解决与数据库并发更新相关的问题，尤其是在使用涉及到特定类型的对象的数据绑定的 UI 时。</span><span class="sxs-lookup"><span data-stu-id="258ad-170">The cloned object can be useful for resolving issues related to concurrent updates to the database, especially where a UI that involves data binding to objects of a certain type is being used.</span></span>  

## <a name="getting-and-setting-the-current-or-original-values-of-complex-properties"></a><span data-ttu-id="258ad-171">获取和设置复杂属性的当前值或原始值</span><span class="sxs-lookup"><span data-stu-id="258ad-171">Getting and setting the current or original values of complex properties</span></span>  

<span data-ttu-id="258ad-172">可以使用属性方法读取和设置整个复杂对象的值，就像它可用于基元属性一样。</span><span class="sxs-lookup"><span data-stu-id="258ad-172">The value of an entire complex object can be read and set using the Property method just as it can be for a primitive property.</span></span> <span data-ttu-id="258ad-173">此外，还可以向下钻取到复杂对象，并读取或设置该对象的属性，甚至是嵌套的对象。</span><span class="sxs-lookup"><span data-stu-id="258ad-173">In addition you can drill down into the complex object and read or set properties of that object, or even a nested object.</span></span> <span data-ttu-id="258ad-174">下面是一些可能的恶意活动：</span><span class="sxs-lookup"><span data-stu-id="258ad-174">Here are some examples:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    // Get the Location complex object
    var location = context.Entry(user)
                       .Property(u => u.Location)
                       .CurrentValue;

    // Get the nested State complex object using chained calls
    var state1 = context.Entry(user)
                     .ComplexProperty(u => u.Location)
                     .Property(l => l.State)
                     .CurrentValue;

    // Get the nested State complex object using a single lambda expression
    var state2 = context.Entry(user)
                     .Property(u => u.Location.State)
                     .CurrentValue;

    // Get the nested State complex object using a dotted string
    var state3 = context.Entry(user)
                     .Property("Location.State")
                     .CurrentValue;

    // Get the value of the Name property on the nested State complex object using chained calls
    var name1 = context.Entry(user)
                       .ComplexProperty(u => u.Location)
                       .ComplexProperty(l => l.State)
                       .Property(s => s.Name)
                       .CurrentValue;

    // Get the value of the Name property on the nested State complex object using a single lambda expression
    var name2 = context.Entry(user)
                       .Property(u => u.Location.State.Name)
                       .CurrentValue;

    // Get the value of the Name property on the nested State complex object using a dotted string
    var name3 = context.Entry(user)
                       .Property("Location.State.Name")
                       .CurrentValue;
}
```  

<span data-ttu-id="258ad-175">使用 OriginalValue 属性而非 CurrentValue 属性来获取或设置原始值。</span><span class="sxs-lookup"><span data-stu-id="258ad-175">Use the OriginalValue property instead of the CurrentValue property to get or set an original value.</span></span>  

<span data-ttu-id="258ad-176">请注意，可以使用属性或 ComplexProperty 方法来访问复杂属性。</span><span class="sxs-lookup"><span data-stu-id="258ad-176">Note that either the Property or the ComplexProperty method can be used to access a complex property.</span></span> <span data-ttu-id="258ad-177">但是，如果想要使用其他属性或 ComplexProperty 调用深化到复杂对象，则必须使用 ComplexProperty 方法。</span><span class="sxs-lookup"><span data-stu-id="258ad-177">However, the ComplexProperty method must be used if you wish to drill down into the complex object with additional Property or ComplexProperty calls.</span></span>  

## <a name="using-dbpropertyvalues-to-access-complex-properties"></a><span data-ttu-id="258ad-178">使用 DbPropertyValues 访问复杂属性</span><span class="sxs-lookup"><span data-stu-id="258ad-178">Using DbPropertyValues to access complex properties</span></span>  

<span data-ttu-id="258ad-179">使用 CurrentValues、OriginalValues 或 GetDatabaseValues 获取实体的所有当前值、原始值或数据库值时，任何复杂属性的值将作为嵌套 DbPropertyValues 对象返回。</span><span class="sxs-lookup"><span data-stu-id="258ad-179">When you use CurrentValues, OriginalValues, or GetDatabaseValues to get all the current, original, or database values for an entity, the values of any complex properties are returned as nested DbPropertyValues objects.</span></span> <span data-ttu-id="258ad-180">然后，可以使用这些嵌套对象获取复杂对象的值。</span><span class="sxs-lookup"><span data-stu-id="258ad-180">These nested objects can then be used to get values of the complex object.</span></span> <span data-ttu-id="258ad-181">例如，以下方法将打印出所有属性的值，包括任何复杂属性的值和嵌套的复杂属性。</span><span class="sxs-lookup"><span data-stu-id="258ad-181">For example, the following method will print out the values of all properties, including values of any complex properties and nested complex properties.</span></span>  

``` csharp
public static void WritePropertyValues(string parentPropertyName, DbPropertyValues propertyValues)
{
    foreach (var propertyName in propertyValues.PropertyNames)
    {
        var nestedValues = propertyValues[propertyName] as DbPropertyValues;
        if (nestedValues != null)
        {
            WritePropertyValues(parentPropertyName + propertyName + ".", nestedValues);
        }
        else
        {
            Console.WriteLine("Property {0}{1} has value {2}",
                              parentPropertyName, propertyName,
                              propertyValues[propertyName]);
        }
    }
}
```  

<span data-ttu-id="258ad-182">若要打印出所有当前属性值，将调用方法，如下所示：</span><span class="sxs-lookup"><span data-stu-id="258ad-182">To print out all current property values the method would be called like this:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    WritePropertyValues("", context.Entry(user).CurrentValues);
}
```  
