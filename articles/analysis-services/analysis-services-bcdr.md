---
title: "Azure Analysis Services 高可用性 | Microsoft Docs"
description: "要确保 Azure Analysis Services 高可用性。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: d71bf041585af101d6aa67ba2697f5192bdfd048
ms.contentlocale: zh-cn
ms.lasthandoff: 05/03/2017


---

# <a name="analysis-services-high-availability"></a>Analysis Services 高可用性
本文说明如何确保 Azure Analysis Services 服务器的高可用性。 


## <a name="assuring-high-availability-during-a-service-disruption"></a>在服务中断过程中确保高可用性
虽然罕见，但是 Azure 数据中心可能会发生服务中断。 发生服务中断时，可能会导致业务中断持续几分钟，也可能持续数小时。 通常，通过服务器冗余实现高可用性。 借助 Azure Analysis Services，可以通过在一个或多个区域中创建附加的辅助服务器实现冗余。 创建冗余服务器时，若要确保这些服务器上的数据和元数据与区域中已脱机的服务器同步，可以执行以下操作：

* 将模型部署到其他区域中的冗余服务器。 此方法要求在主服务器和冗余服务器中并行处理数据，以确保所有服务器同步。

* 从主服务器备份数据库，并在冗余服务器上还原。 例如，可以每夜自动备份到 Azure 存储，并还原到其他区域中的其他冗余服务器。 

在上述任一情况下，如果主服务器发生服务中断，都必须更改报表客户端中的连接字符串，以连接到不同区域数据中心中的服务器。 此更改应视为最后手段，仅在发生灾难性区域数据中心服务中断时适用。 很可能在更新所有客户端上的连接之前，托管主服务器的数据中心服务中断会恢复到联机状态。 



## <a name="related-information"></a>相关信息
[备份和还原](analysis-services-backup.md)   
[管理 Azure Analysis Services](analysis-services-manage.md) 


