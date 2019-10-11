---
title: 通过 NGen 提高启动性能 EF6
author: divega
ms.date: 10/23/2016
ms.assetid: dc6110a0-80a0-4370-8190-cea942841cee
ms.openlocfilehash: c9b5f8a06add9133d30955e3cc97a92e9b189bdf
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182683"
---
# <a name="improving-startup-performance-with-ngen"></a>通过 NGen 改善启动性能
> [!NOTE]
> **仅限 EF6 及更高版本** - 此页面中讨论的功能、API 等已引入实体框架 6。 如果使用的是早期版本，则部分或全部信息不适用。  

.NET Framework 支持为托管应用程序和库生成本机映像，以帮助应用程序的启动速度更快，在某些情况下还会使用更少的内存。 本机映像是通过在执行应用程序之前将托管代码程序集转换为包含本机计算机指令的文件创建的，从而使 .NET JIT （实时）编译器不必在应用程序运行时。  

在版本6之前，EF 运行时的核心库是 .NET Framework 的一部分，并且会为其自动生成本机映像。 从版本6开始，整个 EF 运行时已合并到 EntityFramework NuGet 包中。 本机映像现在必须使用 Ngen.exe 命令行工具生成，才能获得类似的结果。  

经验观察显示 EF 运行时程序集的本机映像可以在应用程序启动时间的1到3秒之间缩短。  

## <a name="how-to-use-ngenexe"></a>如何使用 Ngen.exe  

Ngen.exe 工具的最基本功能是为程序集及其所有直接依赖项 "安装" （即创建和保留到磁盘）本机映像。 下面介绍如何实现此目的：  

1. 以管理员身份打开 "命令提示符" 窗口  
2. 将当前工作目录更改为你要为其生成本机映像的程序集的位置：  

  ``` console
    cd <*Assemblies location*>  
  ```
3. 根据你的操作系统和应用程序的配置，可能需要为32位体系结构、64位体系结构或两者生成本机映像。  

    对于32位运行：  
  ``` console
    %WINDIR%\Microsoft.NET\Framework\v4.0.30319\ngen install <Assembly name>  
  ```
    对于64位运行：
  ``` console
    %WINDIR%\Microsoft.NET\Framework64\v4.0.30319\ngen install <Assembly name>  
  ```

> [!TIP]
> 生成错误体系结构的本机映像是一个非常常见的错误。 如果有疑问，只需为适用于计算机中安装的操作系统的所有体系结构生成本机映像。  

Ngen.exe 还支持其他功能，例如卸载和显示安装的本机映像、对多个映像的生成进行排队等。有关用法的详细信息，请参阅[ngen.exe 文档](https://msdn.microsoft.com/library/6t9t5wcf.aspx)。  

## <a name="when-to-use-ngenexe"></a>何时使用 Ngen.exe  

当决定在基于 EF 版本6或更高版本的应用程序中为哪些程序集生成本机映像时，应考虑以下选项：  

- **主要 EF 运行时程序集 EntityFramework**：基于 EF 的典型应用程序在启动时或在首次访问数据库时，将从该程序集执行大量的代码。 因此，创建此程序集的本机映像将产生最大的启动性能提升。  
- **应用程序使用的任何 EF 提供程序程序集**：与生成这些的本机映像相比，启动时间也有很大的优势。 例如，如果应用程序使用 EF 提供程序进行 SQL Server 则需要为 EntityFramework 生成本机映像。  
- **应用程序的程序集和其他依赖项**：[Ngen.exe 文档](https://msdn.microsoft.com/library/6t9t5wcf.aspx)介绍了选择哪些程序集生成本机映像的一般准则，以及本机映像对安全性的影响、高级选项（如 "硬绑定"）、方案（如在调试时使用本机映像）以及分析方案等。  

> [!TIP]
> 请确保仔细衡量对应用程序的启动性能和总体性能的使用本机映像的影响，并将其与实际要求进行比较。 尽管本机映像通常有助于改善启动性能，但在某些情况下，会降低内存使用率，并非所有方案都将同样受益。 例如，在稳定状态执行（即，应用程序使用的所有方法都被调用了至少一次），JIT 编译器生成的代码实际上会产生比本机映像更好的性能。  

## <a name="using-ngenexe-in-a-development-machine"></a>在开发计算机中使用 Ngen.exe  

在开发期间，.NET JIT 编译器将为经常更改的代码提供最佳总体平衡。 对于已编译的依赖项（如 EF 运行时程序集），生成本机映像可通过在每次执行开始时缩短几秒钟来帮助加速开发和测试。  

查找 EF 运行时程序集的好地方是解决方案的 NuGet 包位置。 例如，对于使用 EF 6.0.2 与 SQL Server 并面向 .NET 4.5 或更高版本的应用程序，你可以在命令提示符窗口中键入以下内容（请记得以管理员身份打开它）：  

```console
cd <Solution directory>\packages\EntityFramework.6.0.2\lib\net45
%WINDIR%\Microsoft.NET\Framework\v4.0.30319\ngen install EntityFramework.SqlServer.dll
%WINDIR%\Microsoft.NET\Framework64\v4.0.30319\ngen install EntityFramework.SqlServer.dll
```  

> [!NOTE]
> 这就充分利用了为 EF 提供程序安装本机映像 SQL Server 的事实，在默认情况下，还会安装主要 EF 运行时程序集的本机映像。 这是因为 Ngen.exe 可以检测到 EntityFramework 是位于同一个目录中 EntityFramework 程序集的直接依赖项。  

## <a name="creating-native-images-during-setup"></a>在安装过程中创建本机映像  

在安装过程中，WiX 工具包支持为托管程序集生成本机映像的排队，如本操作[方法指南](https://wixtoolset.org/documentation/manual/v3/howtos/files_and_registry/ngen_managed_assemblies.html)中所述。 另一种方法是创建执行 Ngen.exe 命令的自定义安装任务。  

## <a name="verifying-that-native-images-are-being-used-for-ef"></a>正在验证是否正在将本机映像用于 EF  

您可以通过查找扩展名为 "ni .dll" 或 ". al.exe" 的加载程序集来验证特定应用程序是否正在使用本机程序集。 例如，EF 的主运行时程序集的本机映像将称为 EntityFramework。 检查进程的加载的 .NET 程序集的一种简单方法是使用[进程资源管理器](https://technet.microsoft.com/sysinternals/bb896653)。  

## <a name="other-things-to-be-aware-of"></a>其他需要注意的问题  

**创建程序集的本机映像不应与在 GAC 中注册程序集[（全局程序集缓存）](https://msdn.microsoft.com/library/yf1d93sz.aspx)混淆**。 Ngen.exe 允许创建不在 GAC 中的程序集的映像，事实上，使用特定 EF 版本的多个应用程序可以共享同一个本机映像。 虽然 Windows 8 可以自动为 GAC 中的程序集创建本机映像，但不建议将 EF 运行时与应用程序一起部署，并且我们不建议在 GAC 中注册它，因为这样会对程序集解析产生负面影响，在其他方面维护应用程序。  
