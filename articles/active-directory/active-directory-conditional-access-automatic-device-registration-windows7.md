<properties
	pageTitle="# 为加入 Windows 7 域的设备配置自动设备注册 | Microsoft Azure"
	description="逐步说明如何将已加入 Windows 7 域的设备配置为自动注册到 Azure AD，以及如何使用软件分发系统（如 System Center Configuration Manager），将设备注册软件包部署到已加入 Windows 7 域的设备中。"
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.date="08/02/2015"
	wacn.date=""/>

# 为加入 Windows 7 域的设备配置自动设备注册

IT 管理员可以将已加入 Windows 7 域的设备配置为自动注册到 Azure AD。若要执行此操作，你必须使用软件分发系统（如 System Center Configuration Manager），将设备注册软件包部署到已加入 Windows 7 域的设备中。请务必通读并满足“将已加入 Windows 域的设备自动注册到 Azure Active Directory”中所列的先决条件。

## 在已加入 Windows 7 域的设备上安装设备注册软件包

Windows 7 的设备注册以可下载的 MSI 包形式提供。必须将此包安装到已加入 Active Directory 域的 Windows 7 计算机上。应该使用软件分发系统（如 System Center Configuration Manager）部署此包。MSI 包使用 /quiet 参数支持标准的无提示安装选项。可在 Microsoft Connect 网站上下载该软件包。在这里，你可以先选择“Windows 7 的工作区加入”，然后再进行下载。

![](./media/active-directory-conditional-access/device-registration-process-windows7.gif)

## Azure Active Directory 中的“工作区加入”
对于已加入 Windows 7 域的设备，设备注册不需要也不提供用户界面。安装到计算机上后，任何登录到计算机的域用户都将自动且无提示地注册到 Azure AD 中的一个设备对象。在 Azure AD 中，物理设备的每个已注册用户都有一个设备对象。

安装程序会在系统上创建一项计划任务，该任务将在用户的上下文中运行并在用户登录时触发。当用户登录完成之后，该任务将以无提示方式向 Azure AD 注册用户和设备。可以在“Microsoft”>“工作区加入”下面的“任务计划程序库”中找到该计划任务。该任务将运行并注册所有登录到计算机的 Active Directory 用户。下图列出了用于自动设备注册的分步过程。

![](./media/active-directory-conditional-access/automatic-device-registration-windows7.png)

1. 用户（信息工作者）使用 Active Directory 域凭据登录到 Windows 7 客户端计算机。
1. 执行“工作区加入”计划任务。
1. 以无提示方式使用 Windows 集成身份验证向 AD FS 对用户进行身份验证。
1. Windows 7 电脑已注册到 Azure AD 中的相应用户。
1. 在 Azure AD 中创建设备对象和证书。该对象表示 user@device。
1. “工作区加入”证书存储在计算机上。

## 取消注册已加入 Windows 7 域的设备

你可以选择取消注册已加入 Windows 7 域的设备，具体操作如下：使用软件分发系统（如 System Center Configuration Manager），从已加入 Windows 7 域的设备中卸载工作区加入软件包。

然后，在 Windows 7 计算机上打开命令提示符并执行以下命令来取消注册设备：
    
    %ProgramFiles%\Microsoft Workplace Join\AutoWorkplace.exe /leave

>[AZURE.NOTE]
>此命令必须在已登录到计算机的每个域用户的上下文中运行。事件查看器和针对已加入 Windows 7 域的设备的错误。

Windows 7 计算机上的 Windows 事件日志将显示与“工作区加入”相关的消息。你可以找到成功和不成功的“工作区加入”事件消息。可以在事件查看器中的“应用程序和服务日志”>“Microsoft 工作区加入”下面找到事件日志。

<!---HONumber=79-->