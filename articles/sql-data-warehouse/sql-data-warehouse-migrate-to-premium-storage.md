<properties
   pageTitle="将现有 Azure SQL 数据仓库迁移到高级存储 | Microsoft Azure"
   description="将 SQL 数据仓库迁移到高级存储的说明"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="happynicolle"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/16/2016"
   ms.author="nicw;barbkess;sonyama"/>

# 迁移到高级存储的详细信息
SQL 数据仓库最近推出了[具有更好的性能可预测性的高级存储][]。现在我们已经准备好将目前在标准存储上的现有数据仓库迁移到高级存储。继续阅读，了解有关如何以及何时进行自动迁移的详细信息。如果你想控制何时发生停机时间，也请继续阅读以了解如何进行自行迁移。

如果你有多个数据仓库，请使用下面的[自动迁移计划][]来确定何时进行迁移。

## 自动迁移的详细信息
默认情况下，我们将在下面的[自动迁移计划][]期间，在你所在地区当地时间的下午 6 点至早上 6 点期间的某个时间，对你的数据库进行迁移。迁移期间，你的现有数据仓库将不可用。预计迁移每个数据仓库中的每 TB 的存储将花费 1 小时。我们还确保在迁移过程中的任何部分都不会向你收取费用。

> [AZURE.NOTE] 迁移期间你将不能使用你的现有数据仓库。迁移完成后，你的数据仓库将回到在线状态。

以下详细信息是 Microsoft 代表你完成迁移进行的步骤，不需要你的参与。对于本示例，假设你在标准存储上的现有数据仓库目前名为“MyDW”。

1.	Microsoft 将“MyDW”重命名为“MyDW\_ DO\_NOT\_USE\_[Timestamp]”
2.	Microsoft 将暂停“MyDW\_ DO\_NOT\_USE\_[Timestamp]”。在此期间 Microsoft 将进行备份。如果在次过程中遇到任何问题，你将看到多次暂停/恢复。
3.	Microsoft 将在步骤 2 进行的备份中，在高级存储上创建一个名为“MyDW”的新数据仓库。还原完成之前，“MyDW”不会出现。
4.	还原完成后，“MyDW”将返回到 DWU 的相同层级，并与迁移之前的已暂停或活动状态相同。
5.	迁移完成后，Microsoft 将删除“MyDW\_DO\_NOT\_USE\_[Timestamp]”
	
> [AZURE.NOTE] 这些设置不会作为迁移的一部分执行：
> 
>	-  需要重新启用数据库级别的审核
>	-  需要重新添加**数据库**级别的防火墙规则。**服务器**级别的防火墙规则不受影响。

### 自动迁移计划
将在下面列出的中断计划期间的某个时间的下午 6 点至早上 6 点（该地区的本地时间）期间进行自动迁移。

| 区域 | 预计开始日期 | 预计结束日期 |
| :------------------ | :--------------------------- | :--------------------------- |
| 澳大利亚东部 | 尚未决定 | 尚未决定 |
| 澳大利亚东南部 | 尚未决定 | 尚未决定 |
| 加拿大中部 | 2016 年 6 月 23 日 | 2016 年 7 月 1 日 |
| 加拿大东部 | 2016 年 6 月 23 日 | 2016 年 7 月 1 日 |
| 美国中部 | 2016 年 6 月 23 日 | 2016 年 7 月 1 日 |
| 中国东部 | 尚未决定 | 尚未决定 |
| 东亚 | 2016 年 6 月 23 日 | 2016 年 7 月 1 日 |
| 美国东部 | 2016 年 6 月 23 日 | 2016 年 7 月 1 日 |
| 美国东部 2 | 2016 年 6 月 23 日 | 2016 年 7 月 1 日 |
| 印度中部 | 2016 年 6 月 23 日 | 2016 年 7 月 1 日 |
| 印度南部 | 2016 年 6 月 23 日 | 2016 年 7 月 1 日 |
| 日本东部 | 尚未决定 | 尚未决定 |
| 日本西部 | 尚未决定 | 尚未决定 |
| 美国中北部 | 尚未决定 | 尚未决定 |
| 欧洲北部 | 尚未决定 | 尚未决定 |
| 美国中南部 | 2016 年 6 月 23 日 | 2016 年 7 月 1 日 |
| 亚洲东南部 | 2016 年 6 月 23 日 | 2016 年 7 月 1 日 |
| 欧洲西部 | 2016 年 6 月 23 日 | 2016 年 7 月 1 日 |
| 美国西部 | 2016 年 6 月 23 日 | 2016 年 7 月 1 日 |

## 自行迁移到高级存储
如果你想控制何时发生停机时间，则可以使用以下步骤将标准存储上的现有数据仓库迁移到高级存储。如果选择自行迁移，则必须在该地区的自动迁移开始之前完成自行迁移，以避免自动迁移导致冲突的任何风险（请参阅[自动迁移计划][]）。

> [AZURE.NOTE] 高级存储的 SQL 数据仓库当前不是异地冗余。这意味着数据仓库迁移到高级存储后，该数据将只保留在当前区域。可行时，异地备份每隔 24 小时就会将你的数据仓库复制到 [Azure 配对区域][]，方便你从异地备份恢复到 Azure 的任何区域。自行迁移可以使用异地备份功能时，我们将在[主要文档网站][]上进行公布。相比之下，自动迁移则没有此项限制。

### 确定存储类型
如果是在以下日期之前创建的数据仓库，则目前使用的是标准存储。

| 区域 | 在此日期之前创建的数据仓库 |
| :------------------ | :-------------------------------- |
| 澳大利亚东部 | 高级存储尚不可用 |
| 澳大利亚东南部 | 高级存储尚不可用 |
| 加拿大中部 | 2016 年 5 月 25 日 |
| 加拿大东部 | 2016 年 5 月 26 日 |
| 美国中部 | 2016 年 5 月 26 日 |
| 中国东部 | 高级存储尚不可用 |
| 东亚 | 2016 年 5 月 25 日 |
| 美国东部 | 2016 年 5 月 26 日 |
| 美国东部 2 | 2016 年 5 月 27 日 |
| 印度中部 | 2016 年 5 月 27 日 |
| 印度南部 | 2016 年 5 月 26 日 |
| 日本东部 | 高级存储尚不可用 |
| 日本西部 | 高级存储尚不可用 |
| 美国中北部 | 高级存储尚不可用 |
| 欧洲北部 | 高级存储尚不可用 |
| 美国中南部 | 2016 年 5 月 27 日 |
| 亚洲东南部 | 2016 年 5 月 24 日 |
| 欧洲西部 | 2016 年 5 月 25 日 |
| 美国西部 | 2016 年 5 月 26 日 |


### 自行迁移说明
如果你想控制自己的停机时间，则可以使用备份/还原自行迁移你的数据仓库。预计迁移的还原部分每个数据仓库中的每 TB 的存储将花费 1 小时。如果想在迁移完成后保持相同的名称，请遵循以下的[重命名解决方法][]。

1.	[暂停][]将进行自动备份的数据仓库
2.	从最新的快照进行[还原][]
3.	删除标准存储上的现有数据仓库。**如果此步骤操作失败，你将需要为两个数据仓库支付费用**

> [AZURE.NOTE] 这些设置不会作为迁移的一部分执行：
> 
>	-  需要重新启用数据库级别的审核
>	-  需要重新添加**数据库**级别的防火墙规则。**服务器**级别的防火墙规则不受影响。

#### 可选操作：重命名解决方法 
同一逻辑服务器上的两个数据库不能具有相同的名称。SQL 数据仓库目前不支持重命名数据仓库。下面的说明将让你了解此项自行迁移不具备的功能（注意：自动迁移没有此项限制）。

对于本示例，假设你在标准存储上的现有数据仓库目前名为“MyDW”。

1.	[暂停][]将进行自动备份的“MyDW”
2.	从最新的快照进行[还原][]，并使用其他名称（例如 MyDWTemp）创建一个新数据库
3.	删除“MyDW”。**如果此步骤操作失败，你将需要为两个数据仓库支付费用**
4.	“MyDWTemp”是新创建的数据仓库，所以在一段时间内备份将不能还原。建议在“MyDWTemp”上继续执行一段时间的操作，然后继续进行步骤 5 和 6.
5.	[暂停][]将进行自动备份的“MyDWTemp”。
6.	从最新的快照“MyDWTemp”进行[还原][]，并使用名称“MyDW”创建一个新数据库。
7.	删除“MyDWTemp”。**如果此步骤操作失败，你将需要为两个数据仓库支付费用**

> [AZURE.NOTE] 这些设置不会作为迁移的一部分执行：
> 
>	-  需要重新启用数据库级别的审核
>	-  需要重新添加数据库级别的防火墙规则

## 后续步骤
如果有任何关于数据仓库的问题，请[创建支持票证][]，并参阅“迁移到高级存储”寻找可能的原因。

<!--Image references-->

<!--Article references-->
[自动迁移计划]: #automatic-migration-schedule
[self-migration to Premium Storage]: #self-migration-to-premium-storage
[创建支持票证]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Azure 配对区域]: ./best-practices-availability-paired-regions.md
[主要文档网站]: ./services/sql-data-warehouse.md
[暂停]: ./sql-data-warehouse-manage-compute-portal.md/#pause-compute
[还原]: ./sql-data-warehouse-manage-database-restore-portal.md
[重命名解决方法]: #optional-rename-workaround

<!--MSDN references-->


<!--Other Web references-->
[具有更好的性能可预测性的高级存储]: https://azure.microsoft.com/zh-cn/blog/azure-sql-data-warehouse-introduces-premium-storage-for-greater-performance/

<!---HONumber=Mooncake_0704_2016-->