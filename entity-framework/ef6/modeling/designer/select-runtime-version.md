---
title: 选择 EF 设计器模型实体框架运行时版本-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 7ace90a6-46f8-4f55-a88c-7cad9620085c
ms.openlocfilehash: 40ad05c1f015e6a51150d04eee8d9aa581d72c33
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414991"
---
# <a name="selecting-entity-framework-runtime-version-for-ef-designer-models"></a>选择 EF 设计器模型实体框架运行时版本
> [!NOTE]
> **仅限 EF6 及更高版本** - 此页面中讨论的功能、API 等已引入实体框架 6。 如果使用的是早期版本，则部分或全部信息不适用。

从 EF6 开始，将以下屏幕添加到 EF 设计器，以便在创建模型时选择想要面向的运行时版本。 当项目中尚未安装最新版本的实体框架时，将显示该屏幕。 如果已安装最新版本，则默认情况下将使用它。

![屏幕](~/ef6/media/screen.png)


## <a name="targeting-ef6x"></a>面向 EF6. x

你可以从 "选择你的版本" 屏幕中选择 "EF6"，将 EF6 运行时添加到你的项目。 添加 EF6 后，你将停止在当前项目中看到此屏幕。

如果已安装旧版 EF，则将禁用 EF6 （因为不能将多个版本的运行时作为同一个项目的目标）。 如果此处未启用 EF6 选项，请按照以下步骤将项目升级到 EF6：

1.  在解决方案资源管理器中右键单击项目，然后选择 "**管理 NuGet 包 ...** "
2.  选择**更新**
3.  选择**EntityFramework** （确保它将更新为所需的版本）
4.  单击“更新”。

 

## <a name="targeting-ef5x"></a>面向 EF5. x

你可以从 "选择你的版本" 屏幕中选择 "EF5"，将 EF5 运行时添加到你的项目。 添加 EF5 后，仍会看到此屏幕，其中 "EF6" 选项处于禁用状态。

如果已安装 EF4 版本的运行时，则会看到屏幕上列出的 EF 版本（而不是 EF5）。 在这种情况下，可以使用以下步骤升级到 EF5：

1.  选择**工具-&gt; 库包管理器-&gt; 程序包管理器控制台**
2.  运行**安装包 EntityFramework-版本 5.0.0**

 

## <a name="targeting-ef4x"></a>面向 EF4. x

可以使用以下步骤将 EF4 运行时安装到项目中：

1.  选择**工具-&gt; 库包管理器-&gt; 程序包管理器控制台**
2.  运行**安装包 EntityFramework-版本 4.3.0**
