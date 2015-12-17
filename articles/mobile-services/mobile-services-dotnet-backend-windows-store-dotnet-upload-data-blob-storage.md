<properties 
	pageTitle="使用移动服务将图像上载到 Blob 存储（Windows 应用商店）| 移动服务" 
	description="了解如何使用移动服务将图像上载到 Azure Blob 存储。" 
	documentationCenter="windows" 
	authors="ggailey777" 
	writer="glenga" 
	services="mobile-services,storage" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.date="06/04/2015" 
	wacn.date=""/>

# 使用移动服务将图像上载到 Azure 存储空间

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../includes/mobile-services-selector-upload-data-blob-storage.md)]

##概述
本主题说明如何借助 Azure 移动服务，使应用程序能够在 Azure 存储空间中上载和存储用户生成的图像。移动服务使用 SQL 数据库存储数据。但是，将二进制大型对象 (BLOB) 数据存储在 Azure Blob 存储服务中可以提高效率。

你无法使用客户端应用程序安全地分发所需的凭据，因此无法安全地将数据上载到 Blob 存储服务。你必须将这些凭据存储在移动服务中，并使用它们来生成用于上载新图像的共享访问签名 (SAS)。移动服务会向客户端应用程序安全返回 SAS（一个凭据，其过期时间较短 &mdash; 在本例中为 5 分钟）。然后，应用程序将使用此临时凭据来上载图像。在此示例中，公众可以从 Blob 服务下载。

在本教程中，你将要向移动服务快速入门应用程序添加功能，使用户能够拍摄照片，并使用移动服务生成的 SAS 将图像上载到 Azure。

##先决条件


本教程需要的内容如下：

+ Microsoft Visual Studio 2013 或更高版本。
+ 为 Microsoft Visual Studio 安装 Nuget Package Manager。
+ [Azure 存储帐户][How To Create a Storage Account]

本教程基于移动服务快速入门。在开始本教程之前，必须先[完成移动服务入门]。

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-blob-storage](../includes/mobile-services-dotnet-backend-configure-blob-storage.md)]

##<a name="install-storage-client"></a>安装 Windows 应用商店应用程序的存储客户端

若要使用 SAS 将应用程序中的图像上载到 Blob 存储，必须先添加 NuGet 包，该包用于安装 Windows 应用商店应用程序的存储客户端库。

1. 在 Visual Studio 的“解决方案资源管理器”中，右键单击客户端应用程序项目，然后选择“管理 NuGet 包”。

2. 在左窗格中，选择“联机”类别，选择“包括预发行版”，搜索 **WindowsAzure.Storage-Preview**，在“Azure 存储空间”包上单击“安装”，然后接受许可协议。

  	![添加 Azure 存储空间 NuGet](./media/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png)

  	随即会将 Azure 存储服务的客户端库添加到项目。

接下来，你要更新快速入门应用程序以捕获和上载图像。

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-upload-to-blob-storage](../includes/mobile-services-windows-store-dotnet-upload-to-blob-storage.md)]

 
<!-- Anchors. -->
[Install the Storage Client library]: #install-storage-client
[Update the client app to capture images]: #add-select-images
[Install the storage client in the mobile service project]: #storage-client-server
[Update the TodoItem definition in the data model]: #update-data-model
[Update the table controller to generate an SAS]: #update-scripts
[Upload images to test the app]: #test
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Send email from Mobile Services with SendGrid]: /documentation/articles/store-sendgrid-mobile-services-send-email-scripts
[Schedule backend jobs in Mobile Services]: /documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks
[完成移动服务入门]: /documentation/articles/mobile-services-windows-store-dotnet-get-started

[Azure Management Portal]: https://manage.windowsazure.cn/
[How To Create a Storage Account]: /documentation/articles/storage-create-storage-account
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866
[Mobile Services .NET How-to Conceptual Reference]: /documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
[Windows Phone SDK 8.0]: http://www.microsoft.com/zh-cn/download/details.aspx?id=35471

<!---HONumber=71-->