---
title: "Azure Service Fabric Docker Compose 预览版 | Microsoft Docs"
description: "Azure Service Fabric 接受 Docker Compose 格式，因此可以更轻松地安排使用 Service Fabric 的现有容器。 这种支持目前处于预览状态。"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/01/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 800f5bacd5197f64968fb1c169ef58330ee75e0d
ms.contentlocale: zh-cn
ms.lasthandoff: 05/12/2017

---


# <a name="compose-application-support-in-service-fabric-preview"></a>Service Fabric 中的 Compose 应用程序支持（预览版）

Docker 使用 [docker-compose.yml](https://docs.docker.com/compose) 文件定义多容器应用程序。 为了让客户轻松地熟练使用 Docker 来安排 Service Fabric 中的现有容器应用程序，我们在平台中增加了对 Docker Compose 的本机预览支持。 Service Fabric 可接受 `docker-compose.yml` 文件的版本 3(+)。 由于这种支持处于预览状态，因此仅支持一部分 Compose 指令。 例如，不支持应用程序升级。 但是，始终可以删除并部署应用程序，而不是对其进行升级。  

若要使用此预览版，需要通过门户安装预览版 SDK（版本 255.255.x.x）。 

> [!NOTE]
> 此功能处于预览状态，且不受支持。

## <a name="using-a-docker-composeyml-file-with-service-fabric-preview"></a>将 docker-compose.yml 文件与 Service Fabric 结合使用（预览版）

通过在 PS 中运行以下命令，根据 docker-compose.yml 文件创建 Service Fabric 应用程序：

```powershell
New-ServiceFabricComposeApplication -ApplicationName fabric:/TestContainerApp -Compose docker-compose.yml [-RepositoryUserName <>] [-RepositoryPassword <>] [-PasswordEnctypted]
```

RepositoryUserName 和 RepoistoryPassword 指容器注册表用户名和密码。

如果使用 Azure CLI 2.0，则运行以下命令：

```bash
az sf compose create --application-id fabric:/TestContainerApp --file docker-compose.yml [ [ --repo-user --repo-pass --encrypted ] | [ --repo-user ] ] [ --timeout ]
```
这些命令会创建一个可通过 Service Fabric Explorer 来监视的 Service Fabric 应用程序（在上一示例中名为 `fabric:/TestContainerApp`）。 指定的 `ApplicationName` 用于通过 PS、Azure CLI 2.0 或通过 SFX 进行的运行状况查询。

若要通过 PS 删除应用程序，请使用以下命令：

```powershell
Remove-ServiceFabricComposeApplication  -ApplicationName fabric:/TestContainerApp
```

若要通过 Azure CLI 2.0 删除应用程序，请使用以下命令：

```bash
az sf compose remove  --application-id TestContainerApp [ --timeout ]
```

若要获取 Compose 应用程序的状态，请在 PS 中使用以下命令：

```powershell
Get-ServiceFabricComposeApplicationStatus -ApplicationName fabric:/TestContainerApp -GetAllPages
```

对于 Azure CLI 2.0，请使用以下命令：

```bash
az sf compose status --application-id TestContainerApp [ --timeout ]
```



## <a name="supported-compose-directives"></a>支持的 Compose 指令

此预览版支持 Compose V3 格式中的一部分配置选项。 支持以下基元：

* 服务->部署->副本    
* 服务->部署->放置->约束    
* 服务->部署->资源->限制 
*         -cpu-shares    
*         -memory
*         -memory-swap
* 服务->命令    
* 服务->环境
* 服务->端口    
* 服务->映像
* 服务->隔离（仅适用于 Windows）
* 服务->日志记录->驱动程序    
* 服务->日志记录->驱动程序->选项
* 卷和部署->卷    

必须将群集设置为强制实施 [Service Fabric 资源调控](service-fabric-resource-governance.md)中所述的资源限制。
所有其他 Docker Compose 指令均不受此预览版支持。 

## <a name="servicednsname-computation"></a>ServiceDnsName 计算

如果 Compose 文件中指定的服务名称是完全限定的域名（也就是说，它包含一个句点“.”），则由 Service Fabric 注册的 DNS 名称为包含句点的 `<ServiceName>`。 如果不是，则 ApplicationName 中的每个路径段都会成为服务 DNS 名称中的域标签，其中第一个路径段成为顶级域标签。 因此，如果指定的应用程序名称为 `fabric:/SampleApp/MyComposeApp`，则 `<ServiceName>.MyComposeApp.SampleApp` 将为已注册的 DNS 名称。

## <a name="differences-between-compose-instance-definition-and-service-fabric-application-model-type-definition"></a>Compose（实例定义）和 Service Fabric 应用程序模型（类型定义）之间的差异

docker-compose.yml 文件描述一组包括属性和配置（例如环境变量和端口）在内的可部署容器。 docker-compose.yml 文件中还指定了放置约束、资源限制和 DNS 名称等部署参数。

[Service Fabric 应用程序模型](service-fabric-application-model.md)使用服务类型和应用程序类型，在此模型中，可以有相同类型的多个应用程序实例（例如，每个客户一个应用程序实例）。 此基于类型的模型支持向运行时注册相同应用程序类型的多个版本。 例如，客户 A 可以有一个使用 1.0 类型的 AppTypeA 实例化的应用程序，而客户 B 可以有另一个使用相同类型和版本实例化的应用程序。 应用程序类型在应用程序清单中定义，应用程序名称和部署参数在应用程序创建时指定。

虽然此模型具有较大灵活性，但我们还是计划支持一种更简单的基于实例的部署模型（其类型隐含在清单文件中）。 在此模型中，每个应用程序都会获取自己独立的清单。 我们将通过添加对 docker-compose.yml 的支持，实现对此模型（采用基于实例的部署格式）的预览支持。


## <a name="next-steps"></a>后续步骤

* 了解 [Service Fabric 应用程序模型](service-fabric-application-model.md)。

