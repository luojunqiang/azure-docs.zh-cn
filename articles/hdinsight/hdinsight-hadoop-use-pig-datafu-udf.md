---
title: "在 HDInsight 上通过 Pig 使用 DataFu"
description: "DataFu 是适用于 Hadoop 的库的集合。 了解如何在 HDInsight 群集上通过 Pig 使用 DataFu。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 0016721a-82be-4773-88ad-91e6b2c21cbb
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/04/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: b9dcb069003c647073c978feb588debb689d560e
ms.contentlocale: zh-cn
ms.lasthandoff: 05/18/2017


---
# <a name="use-datafu-with-pig-on-hdinsight"></a>在 HDInsight 上通过 pig 使用 DataFu

了解如何将 DataFu 与 HDInsight 配合使用。 DataFu 是适用于 Hadoop 上的 Pig 的开放源代码库集合。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。

* Azure HDInsight 群集（基于 Linux 或 Windows）

  > [!IMPORTANT]
  > Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 在 Windows 上停用](hdinsight-component-versioning.md#hdi-version-33-nearing-retirement-date)。

* 基本熟悉[在 HDInsight 上使用 Pig](hdinsight-use-pig.md)

## <a name="install-datafu-on-linux-based-hdinsight"></a>在基于 Linux 的 HDInsight 上安装 DataFu

> [!NOTE]
> DataFu 安装在基于 Linux 的群集版本 3.3 和更高版本上，以及基于 Windows 的群集上。 该程序未安装在早于 3.3 的基于 Linux 的群集上。
>
> 如果你使用的是基于 Linux 的群集版本 3.3 或更高版本，或基于 Windows 的群集，则可跳过本部分。

可从 Maven 存储库下载和安装 DataFu。 使用以下步骤将 DataFu 添加到你的 HDInsight 群集：

1. 使用 SSH 连接到基于 Linux 的 HDInsight 群集。 有关详细信息，请参阅 [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)（对 HDInsight 使用 SSH）。

2. 使用以下命令通过 wget 实用程序下载 DataFu jar 文件，或将链接复制并粘贴到浏览器以开始下载。

    ```
    wget http://central.maven.org/maven2/com/linkedin/datafu/datafu/1.2.0/datafu-1.2.0.jar
    ```

3. 然后，将文件上传到 HDInsight 群集的默认存储。 通过将文件放置在默认存储中，使其可供群集中的所有节点使用。

    ```
    hdfs dfs -put datafu-1.2.0.jar /example/jars
    ```

    > [!NOTE]
    > 前一命令将 jar 存储在 `/example/jars` 中，因为此目录已存在于群集存储上。 可使用 HDInsight 群集存储上的任意位置。

## <a name="use-datafu-with-pig"></a>通过 Pig 使用 DataFu

本部分中的步骤假定你熟悉如何在 HDInsight 上使用 Pig。 有关将 Pig 与 HDInsight 配合使用的详细信息，请参阅[将 Pig 与 HDInsight 配合使用](hdinsight-use-pig.md)。

> [!IMPORTANT]
> 在基于 Linux 的 HDInsight 群集上通过 Pig 使用 DataFu 时，必须先注册 jar 文件。
>
> 如果群集使用 Azure 存储，请使用 `wasb://` 路径。 例如，`register wasb:///example/jars/datafu-1.2.0.jar`。
>
> 如果群集使用 Azure Data Lake Store，请使用 `adl://` 路径。 例如，`register adl://home/example/jars/datafu-1.2.0.jar`。
>
> DataFu 默认情况下已在基于 Windows 的 HDInsight 群集上注册。

通常要为 DataFu 函数定义别名。 例如：

    DEFINE SHA datafu.pig.hash.SHA();

此语句为 SHA 哈希函数定义名为 `SHA` 的别名。 然后，可以在 Pig Latin 脚本中使用此别名生成输入数据的哈希值。 例如，以下代码将输入数据中的名称替换为哈希值：

```piglatin
raw = LOAD '/data/raw/' USING PigStorage(',') AS  
    (name:chararray,
    int1:int,
    int2:int,
    int3:int);
mask = FOREACH raw GENERATE SHA(name), int1, int2, int3;
DUMP mask;
```

如果此代码用于以下输入数据：

    Lana Zemljaric,5,9,1
    Qiong Zhong,9,3,6
    Sandor Harsanyi,0,7,3
    Roko Petkovic,2,6,2
    Tibor Rozsa,8,0,0
    Lea Hrastovsek,6,3,6
    Regina Toth,2,1,2
    Eva Makay,8,9,2
    Shi Liao,4,6,0
    Tjasa Zemljaric,0,2,5

这会生成以下输出：

    (c1a743b0f34d349cfc2ce00ef98369bdc3dba1565fec92b4159a9cd5de186347,5,9,1)
    (713d030d621ab69aa3737c8ea37a2c7c724a01cd0657a370e103d8cdecac6f99,9,3,6)
    (7a5f5abdd281f68168199319d98a1a662535f988d1443b3a3c497010937bac89,0,7,3)
    (a94818e93807e12079c4b35f8f3c8c8ef8e8acd1954e7f0476bc1a3a86fc96a9,2,6,2)
    (894ead4f48af91df7e088241218a23157bede7c52115272e417e95c046d48902,8,0,0)
    (6f99f163af3448fda672087db306f363e27a98a9e49c1f274a0860e303f8aec4,6,3,6)
    (a03de92a28be3c6a984c7a153fa9ed81c0413f76a9401955b5f7e04a5dd0ab9f,2,1,2)
    (6ceab977c8fb48d9ad0dc413e6bc646cabd89f22e7ab97a6b0133f3d225c6013,8,9,2)
    (fa9c436469096ff1bd297e182831f460501b826272ae97e921f5f6e3f54747e8,4,6,0)
    (bc22db7c238b86c37af79a62c78f61a304b35143f6087eb99c34040325865654,0,2,5)

## <a name="next-steps"></a>后续步骤

有关 DataFu 或 Pig 的详细信息，请参阅以下文档：

* [Apache DataFu Pig 指南](http://datafu.incubator.apache.org/docs/datafu/guide.html)。
* [将 Pig 与 HDInsight 配合使用](hdinsight-use-pig.md)

