---
title: "如何配置流分析作业的数据输出 | Microsoft 文档"
description: "配置流分析作业的输出 | 学习路径段。"
keywords: "数据输出、数据移动"
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 3bbea3da-bfce-4af1-a15e-d4b23874034f
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/26/2017
ms.author: jeffstok
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: fde7c0e0b5e2bd0246156bacd81250da106b341a
ms.contentlocale: zh-cn
ms.lasthandoff: 05/01/2017

---

# <a name="how-to-configure-data-outputs-for-stream-analytics-jobs"></a>如何配置流分析作业的数据输出

Azure 流分析作业可以连接到一个或多个数据输出，这些数据输出定义了一个到现有数据接收器的连接。 当流分析作业处理并转换传入数据时，数据输出事件的流将写入到作业输出。

流分析数据输出可用于实时仪表板或警报的源、触发数据移动工作流，或者仅为以后批量处理存档数据。 流分析可以与多个 Azure 服务进行一流集成，在这里进行了详细记录。

若要向你的流分析作业添加输出：

1. 在 [Azure 门户](https://portal.azure.com)中，打开作业并单击“输出”，然后在出现的“输出”边栏选项卡中单击“添加”。
   
    ![添加输出](./media/stream-analytics-add-outputs/1-stream-analytics-add-outputs.png)  
   
2. 在“输出别名”框中为该输出提供一个友好的名称。 此名称以后会用于你的作业查询以引用该输出。  
   
    填充所需连接属性的其余部分以连接到你的输出。  这些字段根据输出类型而变化，在此处进行了详细定义。  
   
    ![选择数据移动类型](./media/stream-analytics-add-outputs/2-stream-analytics-add-outputs.png)  
   
3. 根据输出类型，你可能需要指定序列化或格式化数据的方式。 此处记录了每个输出类型的特定序列化设置。
   
    填充所需连接属性的其余部分以连接到你的数据源。 这些字段根据输入类型和源类型而变化，[创建作业文章](stream-analytics-create-a-job.md)中进行了详细定义。  

> [!Note]
>
> 必须先存在添加到作业的输出元素，然后才能启动作业并开始事件的流动。 例如，如果你使用 Blob 存储作为输出，该作业将不会自动创建存储帐户。 在启动 ASA 作业之前，需要由用户创建该存储帐户。
> 
 

## <a name="get-help"></a>获取帮助
如需进一步的帮助，请尝试我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>后续步骤
* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-get-started.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)


