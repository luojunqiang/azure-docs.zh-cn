---
title: "计划升级到 SQL 数据库 V12 | Microsoft 文档"
description: "介绍升级到 Azure SQL 数据库 V12 版本所涉及的准备工作和限制。"
services: sql-database
documentationcenter: 
author: MightyPen
manager: jhubbard
editor: 
ms.assetid: 8020f904-ad27-40c5-8c59-bdf2e690f77d
ms.service: sql-database
ms.custom: V11
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: genemi
translationtype: Human Translation
ms.sourcegitcommit: 09c2332589b1170b411c6f45f4109fb8048887e2
ms.openlocfilehash: 237aa26a40c34d8fb511881604cb26bf9380322e


---
# <a name="plan-and-prepare-to-upgrade-to-sql-database-v12"></a>规划和准备升级到 SQL 数据库 V12
本主题介绍将 Azure SQL 数据库从 V11 版本升级到 V12 时必须执行的规划和准备工作。

我们提供了新的 [Azure 门户](https://portal.azure.com/)来支持升级到 V12。

下表列出了有关 V12 的其他帮助主题。

| 标题和链接 | 内容说明 |
|:--- |:--- |
| [Azure SQL 数据库功能](sql-database-features.md) |提供 Azure SQL 数据库与 SQL Server 的功能矩阵。 |
| [在 SQL 数据库 V12 中创建数据库](sql-database-get-started.md) |介绍如何在版本 V12 中创建新的 Azure SQL 数据库。 其中介绍了各个选项，而不仅仅是如何创建空数据库。 |

## <a name="plan-ahead"></a>提前规划
以下小节介绍了在采取行动将 Azure SQL 数据库升级到 V12 之前，必须了解的知识和做出的决策。

### <a name="version-clarification"></a>版本说明
本文档涉及从 Microsoft Azure SQL 数据库版本 V11 到 V12 的升级。 更准确地讲，版本号非常类似于 Transact-SQL 语句 **SELECT @@version;** 报告的以下两个值：

* 12.0.2000.8*（或更高的位 V12）*
* 11.0.9228.18 *(V11)*
  * V11 有时也称为 SAWA v2。

### <a name="service-tier-planning"></a>服务层规划
从 V12 开始，Azure SQL 数据库仅支持名为“基本”、“标准”和“高级”的服务层。 V12 不支持 Web 和企业服务层。 因此，如果你打算升级当前位于 Web 或企业服务层的 Azure SQL 数据库，必须决定该数据库要位于哪个新的服务层。

有关基本、标准和高级服务层的详细信息，请参阅：

* [SQL 数据库服务层](sql-database-service-tiers.md)
* [将 SQL 数据库 Web/企业数据库升级到新服务层](sql-database-upgrade-server-portal.md)

### <a name="review-the-geo-replication-configuration"></a>查看异地复制配置
如果已为异地复制配置了 Azure SQL 数据库，则应该先记录其当前配置并停止异地复制，然后才能启动升级准备操作。 升级完成后，必须重新为异地复制配置数据库。

策略是将源保持不变，并在数据库的副本上进行测试。

## <a name="preparation-actions"></a>准备操作
完成规划后，你可以执行以下小节中描述的操作步骤，以准备最后的升级阶段。

链接到本帮助主题顶部的帮助主题中提供了最终升级阶段的详细说明。

### <a name="service-tier-actions"></a>服务层操作
V12 不支持 Web 和企业服务定价层。

如果你的 V11 Azure SQL 数据库是 Web 或企业数据库，则升级过程允许你将数据库切换到受支持的层。 升级过程会根据你的数据库工作负荷历史记录推荐一个层。 但是，你可以根据需要选择任何受支持的层。

在开始升级之前，通过将 V11 数据库从 Web 和企业层切换到其他层，可以减少升级过程中需要执行的步骤数。 可以使用新的 [Azure 门户](https://portal.azure.com/)来实现此目的。

如果不确定要切换到哪个服务层，标准层的 S2 级别可能是合理的初始选择。 更低的任何层具有的资源比 Web 和企业层要少。

### <a name="suspend-geo-replication-during-upgrade"></a>升级过程中暂停异地复制
如果异地复制在数据库上处于活动状态，则无法运行升级到 V12 的步骤。 首先必须重新配置数据库，停止使用异地复制。

升级完成后，可以配置数据库，再次使用异地复制。

### <a name="open-ports-on-an-azure-vm-for-client-connectivity"></a>打开 Azure VM 上用于客户端连接的端口
如果客户端程序连接到 SQL 数据库 V12，而客户端运行在 Azure 虚拟机 (VM) 上，则必须打开 VM 上的以下端口范围：

* 11000-11999
* 14000-14999

单击[此处](sql-database-develop-direct-route-ports-adonet-v12.md)可了解有关 SQL 数据库 V12 的端口的详细信息。 SQL 数据库 V12 中的性能增强功能需要这些端口。

## <a name="a-idlimitationsalimitations-during-and-after-upgrade-to-v12"></a><a id="limitations"></a>升级到 V12 期间和之后的限制
### <a name="portals-for-v12"></a>V12 的门户
Azure 有三个门户，每个门户针对 SQL 数据库 V12 提供不同的功能。

* [http://portal.azure.com/](https://portal.azure.com/)<br/>此 Azure 门户是新门户，仍处于预览状态。 此门户尚未完全正式发布 (GA)。 此门户：
  
  * 可以管理 V12 服务器和数据库。
  * 可以将 V11 数据库升级到 V12。
* [http://manage.windowsazure.com/](http://manage.windowsazure.com/)<br/>此 Azure 经典门户最终可能会淘汰。 此门户：
  
  * 可以管理 V12 服务器和数据库。
  * *无法*将 V11 数据库升级到 V12。
* (http://*yourservername*.database.windows.net)<br/>Azure SQL 数据库经典门户：
  
  * *无法*管理 V12 服务器。

我们建议你使用 Visual Studio 2015 (VS2015) 连接到 Azure SQL 数据库。 VS2015 可用于如下所述的任务：

* 运行 Transact-SQL 语句。
* 设计架构。
* 联机或脱机开发数据库。

或者可以免费下载 [Visual Studio Community 2015](https://www.visualstudio.com/vs/community/)，它是具有完整功能的 VS2015 版本。

在旧版 Azure 经典门户上的数据库页中，可以单击“在 Visual Studio 中打开”，在计算机上启动 VS2015，以便与 Azure SQL 数据库建立连接。

另一种方法是使用装有 [CU6](http://support.microsoft.com/kb/3031047/) 的 SQL Server Management Studio (SSMS) 2014 来连接到 Azure SQL 数据库。 此博客文章提供了更多详细信息：<br/>[Azure SQL 数据库的客户端工具更新](https://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/)。

> [!IMPORTANT]
> 建议始终使用最新版本的 Management Studio 以保持与 Microsoft Azure 和 SQL 数据库的更新同步。 [更新 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。
> 
> 

### <a name="limitation-during-upgrade-to-v12"></a>升级到 V12 *期间*的限制
在升级到 V12 期间，V11 数据库仍然支持数据访问。 但你要考虑到几个限制。

| 限制 | 说明 |
|:--- |:--- |
| 升级持续时间 |升级持续时间取决于服务器中数据库的大小、版本和数量。 对于服务器（特别是具有满足以下条件的数据库的服务器），升级过程可以运行几个小时到几天：<br/><br/>*大于 50 GB，或<br/>*位于非高级服务层<br/><br/>升级期间在服务器上创建新数据库可能还会延长升级持续时间。 |
| 没有异地复制 |目前，从 V11 升级的 V12 服务器不支持异地复制。 |
| 在升级到 V12 的最后一个阶段，数据库将暂时不可用 |在升级过程中，属于 V11 服务器的数据库将保持可用。 但是，与服务器和数据库的连接，在最后一个阶段当开始从 V11 切换到准备就绪的 V12 时，暂时不可用。<br/><br/>切换期间的范围是 40 秒到 5 分钟。 对于大多数服务器，切换应在 90 秒内完成。 对于具有大量数据库的服务器，或者当数据库有大量写入工作负荷时，切换时间会增加。 |

### <a name="limitation-after-upgrade-to-v12"></a>升级到 V12 *之后*的限制
| 限制 | 说明 |
|:--- |:--- |
| 无法还原到 V11 |执行就地升级之后，无法还原或撤消升级结果。 |
| Web 或企业层 |在升级开始后，新 V12 数据库的服务器不再能够识别或接受 Web 或企业服务层。 |

### <a name="export-and-import-after-upgrade-to-v12"></a>升级到 V12 *之后*的导出和导入操作
可以使用 [Azure 门户](https://portal.azure.com/)导出或导入 V12 数据库。 或者使用下列任一工具进行导出或导入：

* SQL Server Management Studio (SSMS)
* Visual Studio 2015
* 数据层应用程序框架 (DacFx)

但是，若要使用这些工具，必须先具有或安装其最新更新，以确保它们支持新的 V12 功能：

* [SQL Server Management Studio 2014 累积更新 6](http://support2.microsoft.com/kb/3031047)
* [Visual Studio 2013 中的 SQL Server 数据库工具 2015 年 2 月更新版](https://msdn.microsoft.com/data/hh297027)
* [Azure SQL 数据库 V12 数据层应用程序框架 (DacFx) 2015 年 2 月版](http://www.microsoft.com/download/details.aspx?id=45886)

> [!NOTE]
> 前面的工具链接已在 2015 年 3 月 2 日或之后更新。 我们建议你使用这些工具的较新更新版。
> 
> 

### <a name="restore-to-v12-of-a-deleted-v11-database"></a>将已删除的 V11 数据库还原到 V12
以下方案说明了可以将已删除的 V11 Azure SQL 数据库还原到 V12 Azure SQL 数据库服务器。

1. 假设你有一个 V11 Azure SQL 数据库服务器。 <br/> 在该服务器中，有一个数据库位于已弃用的 Web 和企业服务层上。
2. 删除该数据库。
3. 将服务器升级到 V12。
4. 接下来，将该数据库还原到服务器。 <br/> 这样，该数据库便会升级到 V12，并位于标准服务层的 S0 级别上。
5. 如果 S0 不是你的首选级别，你可以将数据库切换到任何受支持的服务层。

### <a name="powershell-cmdlets"></a>PowerShell cmdlet
可以使用 PowerShell cmdlett 来启动、停止或监视从 Azure SQL 数据库 V11 或其他任何低于 V12 的版本到 V12 的升级。

* [使用 PowerShell 升级到 SQL 数据库 V12](sql-database-upgrade-server-powershell.md)

有关这些 PowerShell cmdlet 的参考文档，请参阅：

* [Get-AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
* [Start-AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
* [Stop-AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)

Stop- cmdlet 表示取消，而不是暂停。 你无法在中途恢复升级，而只能从头开始重新升级。 Stop- cmdlet 将清理并释放所有相应的资源。

## <a name="failure-resolution"></a>失败解决方法
如果任何奇怪的原因导致升级失败，V11 数据库将保持活动状态并像平常一样工作。

## <a name="related-links"></a>相关链接
* Microsoft Azure [预览版功能](https://azure.microsoft.com/services/preview/)

<!--Anchors-->
[Subheading 1]: #subheading-1



<!--HONumber=Dec16_HO1-->

